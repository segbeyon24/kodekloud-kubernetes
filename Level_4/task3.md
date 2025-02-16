## Task

The Nautilus Application Development team is planning to deploy one of the php-based applications on Kubernetes cluster. 
As per the recent discussion with DevOps team, they have decided to use nginx and phpfpm. 
Additionally, they also shared some custom configuration requirements. Below you can find more details. 
Please complete this task as per requirements mentioned below:

1) Create a service to expose this app, the service type must be NodePort, nodePort should be 30012.

2.) Create a config map named nginx-config for nginx.conf as we want to add some custom settings in nginx.conf.

a) Change the default port 80 to 8093 in nginx.conf.

b) Change the default document root /usr/share/nginx to /var/www/html in nginx.conf.

c) Update the directory index to index  index.html index.htm index.php in nginx.conf.

3.) Create a pod named nginx-phpfpm .

b) Create a shared volume named shared-files that will be used by both containers (nginx and phpfpm) also it should be a emptyDir volume.

c) Map the ConfigMap we declared above as a volume for nginx container. Name the volume as nginx-config-volume, mount path should be 
/etc/nginx/nginx.conf and subPath should be nginx.conf

d) Nginx container should be named as nginx-container and it should use nginx:latest image. PhpFPM container should be named as php-fpm-container 
and it should use php:8.2-fpm-alpine image.

e) The shared volume shared-files should be mounted at /var/www/html location in both containers. Copy /opt/index.php from jump host to the nginx 
document root inside the nginx container, once done you can access the app using App button on the top bar.

Before clicking on finish button always make sure to check if all pods are in running state.
You can use any labels as per your choice.

 

## Solution

1. Create the resources:

```yaml
apiVersion: v1
kind: ConfigMap
metadata: 
  name: nginx-config
data:
  nginx.conf: | 
    user nginx;
    worker_processes auto;
    error_log /var/log/nginx/error.log warn;
    pid /var/run/nginx.pid;

    events {
        worker_connections 1024;
    }

    http {
        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        sendfile on;
        keepalive_timeout 65;

        server {
            listen 8093;
            server_name localhost;

            root /var/www/html;
            index index.html index.htm index.php;

            location / {
                try_files $uri $uri/ =404;
            }

            location ~ \.php$ {
                fastcgi_pass 127.0.0.1:9000;
                fastcgi_index index.php;
                fastcgi_param SCRIPT_FILENAME /var/www/html$fastcgi_script_name;
                include fastcgi_params;
            }
        }
    }

---
apiVersion: v1
kind: Service
metadata:
  name: php-service
spec:
  selector:
    app: php-app
  type: NodePort
  ports:
    - protocol: TCP
      port: 8093
      targetPort: 8093
      nodePort: 30012

---
apiVersion: v1
kind: Pod
metadata:
  name: nginx-phpfpm
  labels: 
    app: php-app

spec:
  volumes:
    - name: shared-files
      emptyDir: {}
    - name: nginx-config-volume
      configMap:
        name: nginx-config

  containers:
  - name: nginx-container
    image: nginx:latest
    volumeMounts:
      - name: nginx-config-volume
        mountPath: /etc/nginx/nginx.conf
        subPath: nginx.conf
      - name: shared-files
        mountPath: /var/www/html
    ports:
      - containerPort: 8093
  
  - name: php-fpm-container
    image: php:8.2-fpm-alpine
    volumeMounts:
      - name: shared-files
        mountPath: /var/www/html
```


2. Copy file to nginx container:
```bash
kubectl cp /opt/index.php nginx-phpfpm:/var/www/html/index.php -c nginx-container
```



## Notes [Asking AI]

1. How will the nginx-container access/use this copied file? 

The nginx-container accesses the copied index.php file because both the nginx-container and php-fpm-container 
share the shared-files volume, which is mounted at /var/www/html. 
Since nginx.conf is configured to serve PHP files via FastCGI (fastcgi_pass 127.0.0.1:9000), 
when a request for index.php comes in, Nginx forwards it to the php-fpm-container, which executes the PHP script 
and returns the response to Nginx for serving.


2. Why is nginx-container needed here? Why didn't I just use the php-fpm-container?

The nginx-container is needed because PHP-FPM (php-fpm-container) does not serve HTTP requests directly—it only processes PHP scripts.
Here’s why both are used:

- Nginx (nginx-container)
  - Acts as the web server, handling HTTP requests.
  - Serves static files like HTML, CSS, and JS.
  - For PHP requests (.php files), it forwards them to PHP-FPM via FastCGI.

- PHP-FPM (php-fpm-container)
  - Only processes PHP scripts; it doesn’t serve static files or handle HTTP.
  - Listens for FastCGI requests from Nginx and executes PHP code.

If you used only php-fpm-container, you’d need another service (like php -S or Apache) to serve HTTP requests. 
Nginx is optimized for handling static files and reverse proxying, making it the best choice in this setup. 



3. Were the adjustments to the nginx,conf necessary? 
Yes, the adjustments to nginx.conf were necessary because:

- Changing listen 80 to listen 8093
  - Required to match the exposed service port (NodePort 30012 → ContainerPort 8093).
  - Ensures Nginx listens on the correct internal port.

- Changing document root from /usr/share/nginx to /var/www/html
  - Needed because php-fpm-container expects PHP files in /var/www/html.
  - Matches the shared volume mount path, allowing both containers to access index.php.

- Updating index directive to index.html index.htm index.php
  - Ensures index.php is recognized and passed to PHP-FPM.

- Adding FastCGI configuration (location ~ \.php$ {})
  - Essential for forwarding PHP requests to PHP-FPM (php-fpm-container).
  - Without this, Nginx would try to serve PHP files as plain text.



