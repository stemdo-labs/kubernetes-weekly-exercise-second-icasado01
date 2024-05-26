# Ejercicio semanal de Kubernetes y Helm
**Autor**: icasado01

## Introducción
El proyecto consiste en el despliegue de un cluster de kubernetes con **Laravel**, **MySQL**, y **phpMyAdmin** (este último siendo opcional). Todo ello empaquetado en un chart de helm, el cual tiene valores modificables.

## Estructura del chart
La estructura del chart es la siguiente:
```
├── charts
├── Chart.yaml
├── README.md
├── templates
│   ├── laravel.yaml
│   ├── mysql.yaml
│   ├── namespace.yaml
│   ├── NOTES.txt
│   ├── phpmyadmin.yaml
│   └── secrets.yaml
└── values.yaml
```

Cada despliegue tiene su propio fichero YAML en el que se definen todos los requisitos necesarios para su funcionamiento (Deployment, Services, Volumes...). Pueden modificarse ciertos aspectos de los despliegues en el fichero values.yaml.

### Laravel
El despliegue de Laravel se realiza mediante el archivo laravel.yaml en el directorio de templates. Se utilizará la imagen `bitnami/laravel`, con la versión que indiquemos en el values.

### MySQL
El despliegue de MySQL se realiza mediante el archivo laravel.yaml en el directorio de plantillas. Se utilizará la imagen oficial de MySQL con la versión que indiquemos en el values.

### phpMyAdmin
El despliegue de phpMyAdmin es opcional, se indicará si se quiere desplegar o no en el fichero values con true si se desea desplegar y con false si no se desea hacer el despliegue:
```
phpmyadmin:
  enable: true/false
```
## Ejecución
Para desplegar la aplicación, ejecuta el siguiente comando dentro del directorio del chart:
```
helm install -f values.yaml [nombre_release] .
```
### Ejemplos de values
Se proporcionan dos ejemplos de values, un values que despliega solo la aplicación de Laravel (junto con MySQL) y otro que despliega la aplicación de Laravel y phpMyAdmin
#### values-laravel.yaml
No despliega la aplicación de phpMyAdmin
```
namespace: laravel

laravel:
  image:
    version: "11.0.8"
  replicas: "1"
  pvstorage: 1Gi
  pvcstorage: 1Gi
  path: "/mnt/data"
  requests:
    cpu: 200m
    memory: 512Mi
  limits:
    cpu: 200m
    memory: 880Mi
  ingress:
    host: "laravel.weekly.com"
    path: "/"

mysql:
  image:
    version: "8.4"
  replicas: "1"
  pvstorage: 1Gi
  pvcstorage: 1Gi
  db:
    user: nacho
    password: secret
    root_password: root
    dbname: laravel

phpmyadmin:
  enabled: false # Define si se despliega el phpmyadmin
```
#### values-laravel-pma.yaml
En este ejemplo, si que se despliega phpMyAdmin
```
namespace: laravel

laravel:
  image:
    version: "11.0.8"
  replicas: "1"
  pvstorage: 1Gi
  pvcstorage: 1Gi
  path: "/mnt/data"
  requests:
    cpu: 200m
    memory: 512Mi
  limits:
    cpu: 200m
    memory: 875Mi
  ingress:
    host: "laravel.weekly.com"
    path: "/"

mysql:
  image:
    version: "8.4"
  replicas: "1"
  pvstorage: 1Gi
  pvcstorage: 1Gi
  db:
    user: nacho
    password: secret
    root_password: root
    dbname: laravel


phpmyadmin:
  enabled: true # Define si se despliega el phpmyadmin
  image:
    version: "5.2.1"
  requests:
    cpu: 200m
    memory: 512Mi
  limits:
    cpu: 200m
    memory: 875Mi
  ingress:
    host: "phpmyadmin.weekly.com"
    path: "/"
```
