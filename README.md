## Link Video Sustentación: https://youtu.be/bUZKcVq7VSo

# ST0263-TOPICOS ESPECIALES EN TELEMATICA

## Estudiante(s): Esteban Trujillo Carmona, etrujillo@eafit.edu.co

## Profesor: Edwin Nelson Montoya Munera, emontoya@eafit.edu.co


# Reto 1 y 2
#
# 1. Breve descripción de la actividad

Red P2P No estructurada basada en servidor central. En la cual cada proceso tiene uno o más microservicios que componen un sistema de compartición de archivos distribuido y descentralizado.  

## 1.1. Que aspectos cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)
### Requisitos funcionales

- Autenticación de peers (login, logout)
- Consulta de recursos (sendIndex)
- Servicio de transferencia de archivos (download, upload)
- Servicio de cambio de moneda (currency exchange)

### Requisitos no funcionales

- Escalabilidad
- Rendimiento
- Mantenibilidad
- Disponibilidad

## 1.2. Que aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)


# 2. información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas.

### Mejores Prácticas

- Distribución modular: El sistema se organizó de tal manera que todos los servicios y módulos de clientes y server grpc, tuvieran su propio archivo y carpeta, para que sea más fácil localizar los archivos a la hora de hacer cambios.
- Escalabilidad horizontal: Esto nos permite agregar tantos peers como queramos fácilmente.
- Gestión de configuraciones: Toda la información sensible como ips o puertos quedó guardada en un archivo de configuración y no quemada dentro del código.
- Tolerancia a fallos: 

# 3. Descripción del ambiente de desarrollo y técnico: lenguaje de programación, librerias, paquetes, etc, con sus numeros de versiones.

## Como se compila y ejecuta.

Primero se deben crear las instancias correpondientes en AWS, de central-server, peer1 y peer2. Preferiblemente con ip's elásticas.

Una vez ya creadas, se debe acceder a cada una de ellas remotamente mediante SSH y se clona el repositorio con el proyecto. Además se debe instalar docker, para luego poder correr los contenedores.

Se realizan los cambios en el _docker-compose.yml_ de las instancias de los peers, para especificar el puerto, y en los archivos .env para espcificar las ip's expuestas y la del servidor central. Adicionalmente se debe modificar el archivo .env del servidor central, ya que en este se especifica el puerto por donde este va a estar escuchando.

Para editar el docker-compose.yml en las instancias de peer1 y peer2
```
vi docker-compose.yml
```

Para editar el archivo .env (en central_server, peer1 y peer2)
```
touch .env
vi .env
```

Luego de crear los archivos de configuración, queda hacer _build_ y correr la parte del contenedor específica para cada isntancia.

Para central-server:
```
docker compose build 
docker compose up
```

Para el PServer de peer1 y peer2: 

```
docker compose build
docker compose up pserver
```

Para el PClient de peer1 y peer2:

```
docker compose run -i pclient
```

Con esto ya todas las instancias están corriendo y se pueden realizar las pruebas por medio del CLI de los PClients de los peers. 


## Detalles del desarrollo.

El servidor central fue desarrollado en Golang y tanto el PCliente como el PServidor fueron desarrollados en Python. Se desplegaron tres instancias EC2 de AWS, una para el servidor central (central-server), y dos para peers (peer1 y peer2).

## Detalles técnicos

Endpoints: 

## Descripción y como se configura los parámetros del proyecto (ej: ip, puertos, conexión a bases de datos, variables de ambiente, parámetros, etc)

Como ya fue mencionado las ip's se configuran en los archivos de _docker-compose.yml_ y en los archivos _.env_, lo mismo para las variables de entorno. En cuanto a bases de datos, no fueron utilizadas para este proyecto. 


## Opcional - detalles de la organización del código por carpetas o descripción de algún archivo. (ESTRUCTURA DE DIRECTORIOS Y ARCHIVOS IMPORTANTE DEL PROYECTO, comando 'tree' de linux)
## 

#### Video

link:  https://youtu.be/QJnk5angS9A
