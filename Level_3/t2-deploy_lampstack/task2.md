
## Task - Deploy Lamp Stack on Kubernetes Cluster


The Nautilus DevOps team want to deploy a PHP website on Kubernetes cluster. They are going to use Apache as a web server and Mysql for database. The team had already gathered the requirements and now they want to make this website live. Below you can find more details:


1) Create a config map php-config for php.ini with variables_order = "EGPCS" data.


2) Create a deployment named lamp-wp.


3) Create two containers under it. First container must be httpd-php-container using image webdevops/php-apache:alpine-3-php7 and second container must be mysql-container from image mysql:5.6. Mount php-config configmap in httpd container at /opt/docker/etc/php/php.ini location.


4) Create kubernetes generic secrets for mysql related values like myql root password, mysql user, mysql password, mysql host and mysql database. Set any values of your choice.


5) Add some environment variables for both containers:


a) MYSQL_ROOT_PASSWORD, MYSQL_DATABASE, MYSQL_USER, MYSQL_PASSWORD and MYSQL_HOST. Take their values from the secrets you created. Please make sure to use env field (do not use envFrom) to define the name-value pair of environment variables.


6) Create a node port type service lamp-service to expose the web application, nodePort must be 30008.


7) Create a service for mysql named mysql-service and its port must be 3306.


8) We already have /tmp/index.php file on jump_host server.


a) Copy this file into httpd container under Apache document root i.e /app and replace the dummy values for mysql related variables with the environment variables you have set for mysql related parameters. Please make sure you do not hard code the mysql related details in this file, you must use the environment variables to fetch those values.


b) You must be able to access this index.php on node port 30008 at the end, please note that you should see Connected successfully message while accessing this page.



## Solution

1. Create deployment (and other resources in the yaml file)
```bash
k apply -f lamp-wp.yaml
```

2. Copy index.php in the jumphost to the httpd container
```bash
k cp /tmp/index.php <pod_name>:/app
```

3. Change the dummy mysql environment variables in the index.php of the container app/index.php:
```php
$dbname = getenv('MYSQL_DATABASE');
$dbuser = getenv('MYSQL_USER');
$dbpass = getenv('MYSQL_PASSWORD');
$dbhost = getenv('MYSQL_HOST');
```

4. access the app on the browser `http://<node_IP>:30008:index.php`



## The deployed resources

1. Configmap - php-config
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: php-config
data:
  php.ini: |
    variables_order = "EGPCS"
```

2. Secret - mysql-secret
```yaml
apiVersion: v1
kind: Secret
metadata:
  name: mysql-secret
  labels:
    app: lamp-wp
type: Opaque
data:
  MYSQL_ROOT_PASSWORD: cGFzc3dvcmQ=
  MYSQL_DATABASE: d29yZHByZXNz
  MYSQL_USER: dXNlcg==
  MYSQL_PASSWORD: cGFzc3dvcmQ=
  MYSQL_HOST: bXlzcWwtc2VydmljZQ==
```

3. Deployment - lamp-wp
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: lamp-wp
  labels:
    app: lamp-wp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: lamp-wp
  template:
    metadata:
      labels:
        app: lamp-wp
    spec:
      containers:
        - name: httpd-php-container
          image: webdevops/php-apache:alpine-3-php7
          volumeMounts:
            - name: php-config
              mountPath: /opt/docker/etc/php/php.ini
              subPath: php.ini
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_ROOT_PASSWORD
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_DATABASE
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_USER
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_PASSWORD
            - name: MYSQL_HOST
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_HOST
        - name: mysql-container
          image: mysql:5.6
          env:
            - name: MYSQL_ROOT_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_ROOT_PASSWORD
            - name: MYSQL_DATABASE
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_DATABASE
            - name: MYSQL_USER
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_USER
            - name: MYSQL_PASSWORD
              valueFrom:
                secretKeyRef:
                  name: mysql-secret
                  key: MYSQL_PASSWORD
      volumes:
        - name: php-config
          configMap:
            name: php-config
```

4. Service - httpd
```yaml
apiVersion: v1
kind: Service
metadata:
  name: lamp-service
spec:
  type: NodePort
  selector:
    app: lamp-wp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30008
```

5. Service - mysql
```yaml
apiVersion: v1
kind: Service
metadata:
  name: lamp-service
spec:
  selector:
    app: lamp-wp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30008
```