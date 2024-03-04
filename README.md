## Link Video Sustentación: 👉👉 https://youtu.be/bUZKcVq7VSo
[![Video sustentacion](https://img.youtube.com/vi/bUZKcVq7VSo/0.jpg)](https://youtu.be/bUZKcVq7VSo)

# ST0263-TÓPICOS ESPECIALES EN TELEMATICA

- **Estudiante(s):** Esteban Trujillo Carmona, etrujillo@eafit.edu.co
- **Profesor:** Edwin Nelson Montoya Múnera, emontoya@eafit.edu.co

# Reto 1 y 2

# 1. Breve descripción de la actividad.

Esencialmente, la actividad consistió en la construcción de una red P2P No estructurada basada en servidor central, en la cual cada proceso tiene uno o más microservicios que componen un sistema de compartición de archivos distribuido y descentralizado. Ademmás, se utilizaron diferentes tipos de comunicación o middlewares como gRPC y REST API.   

## 1.1. Qué aspectos cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)
### Requisitos funcionales.

- Autenticación de peers (login, logout).
- Consulta de recursos (sendIndex).
- Servicio de transferencia de archivos (download, upload)
- Servicio de cambio de moneda (currency exchange).
- Rotación de las responsabilidades (Round Robin).
- Servicio de Bootstrap o inicialización de los nuevos peers a la red P2P.
- Implementación de los tipos de comunicación tanto REST API como gRPC.
- Implementación de Docker para el trabajo tanto del servidor central como de los peers.
- Despliegue del reto en AWS Academy.

### Requisitos no funcionales.

- Escalabilidad debido a la estructuración y a la arquitectura definida. 
- Rendimiento debido a la separación de los peers y servidor central en instancias/máquinas separadas, una para cada uno de ellos.
- Disponibilidad en tanto el servidor central y los pservers estaban siempre pendientes de cualquier query que pudieran hacerles a ellos.
- Concurrencia en la respuesta de las requests por parte del servidor central y de los pservers de los peers.

## 1.2. Qué aspectos NO cumplió o desarrolló de la actividad propuesta por el profesor (requerimientos funcionales y no funcionales)
- No se implementó MOM en tanto el profesor recomendó finalmente no hacerlo.
- No se hace la transferencia periódica del índice de archivos de manera independiente, sino que se transfiere cada que un peer realiza alguna acción dentro de la red, es decir, cuando hay alguna actualización.
- No se realizó lo relacionado con el peer titular y peer suplente, en tanto el profesor recomendó finalmente no hacerlo.

# 2. Información general de diseño de alto nivel, arquitectura, patrones, mejores prácticas utilizadas.

### Arquitectura:
- Se tiene una arquitectura Peer-to-Peer, con la peculiaridad de poseer de igual manera un servidor central. A continuación se muestra unas imágenes de la arquitectura:
#### Arquitectura de la red (bosquejo).
![image](https://github.com/EsteTruji/etrujilloc-st0263/assets/82886890/3314db74-927c-41e3-b6fb-265b8d905e0b)

#### Arquitectura de la solución.
![image](https://github.com/EsteTruji/etrujilloc-st0263/assets/82886890/0fdcd573-d809-4842-9d17-6124b5d319bd)

### Mejores Prácticas.

- Distribución modular: El sistema se organizó de tal manera que todos los servicios y módulos de clientes y server grpc, tuvieran su propio archivo y carpeta, para que sea más fácil localizar los archivos a la hora de hacer cambios.
- Escalabilidad horizontal: Esto nos permite agregar tantos peers como queramos fácilmente.
- Gestión de configuraciones: Toda la información sensible como ips o puertos quedó guardada en un archivo de configuración y no quemada dentro del código.

### Patrones/Principios.
- Se utilizó el principio DRY (Don't Repeat Yourself), en tanto se procuró no duplicar segmentos de código que podían ser llevados a otras carpetas y ser usados desde allí cada vez que se necesitara.

# 3. Descripción del ambiente de desarrollo y técnico: lenguaje de programación, librerías, paquetes, etc, con sus números de versiones.

## Lenguajes de programación.
Se utilizaron los lenguajes Python (para el peer, tanto pserver como pclient), y Go (para el servidor central).

## Librerías/paquetes.
Las librerías utilizadas fueron:
- **Python:** os, sys, grpc, futures, requests, cmd, json, grpcio, grpcio-tools, protobuf, python-dotenv.
- **Go:** errors, bcrypt, jwt, fmt, os, time, regexp, strings, net/http, strconv, gin. 

## Cómo se compila y ejecuta.

Primero se deben crear las instancias correpondientes en AWS, de central-server, peer1 y peer2. Preferiblemente con IPs elásticas.

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

Luego de crear los archivos de configuración, queda hacer _build_ y correr la parte del contenedor específica para cada instancia.

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

El servidor central fue desarrollado en Golang y tanto el PCliente como el PServidor fueron desarrollados en Python. Se desplegaron tres instancias EC2 de AWS, una para el servidor central (central-server), y dos para peers (peer1 y peer2). A cada una de esas instancias se le asignó una IP elástica para evitar el cambio de la misma cada vez que se apagara o reiniciara alguna de ellas, o el Learner Lab en sí. Todos los elementos y componentes del proyecto (servidor central, peer) fueron completamente contenerizados usando Docker.

## Detalles técnicos
### Servicios:
En este sistema se ofrecen los siguientes servicios:
- **Query:** Consiste en realizar una consulta al servidor central para saber si existe determinado archivo en la red P2P, y en el caso de que sí exista, mande la dirección del peer en el cual se encuentra.
- **Download:** Consiste en realizar una consulta al servidor central para, primero, saber si existe determinado archivo en la red P2P, segundo, en el caso de que sí exista, mande la dirección del peer en el cual se encuentra, y tercero, permita la descarga de dicho archivo en el peer propio.
- **Upload:** Consiste en realizar una subida de un archivo determinado a alguno de los peer de la red, siguiendo la directriz del Round Robin.
- **Currency converter:** Consiste en realizar la conversión de un determinado valor de una moneda específica, a otra diferente.

### Endpoints.
- /login: dedicado para llevar a cabo el login de un peer a la red P2P.
- /logout: dedicado para llevar a cabo el logout de un peer de la red P2P.
- /sendIndex: dedicado para enviar al servidor central los índices de los archivos que posee actualmente un determinado peer.
- /indexTable: dedicado para obtener la tabla de los índices de archivos existentes en la red P2P.
- /query: dedicado para realizar la consulta de que si existe un determinado archivo en la red, y quién tendría ese archivo en caso tal de que sí existiera.
- /getPeerUploading: dedicado para obtener un peer, siguiendo la secuencia dada por el Round Robin, con el fin de hacer un upload a este, o acceder a un servicio determinado que este brinda.
  
## Descripción y cómo se configura los parámetros del proyecto (ej: ip, puertos, conexión a bases de datos, variables de ambiente, parámetros, etc)

### IPs y puertos.
En primer lugar, para configurar las IPs y puertos, es necesario ingresar a los archivos _docker-compose.yml_ (donde se debe configurar tanto el puerto del servidor central y del peer) y en los archivos _.env_ (donde se debe configurar nuevamente el puerto y la IP del servidor central y el peer). 

### Variables de entorno/ambiente.
En segundo lugar, con relación a las variables de entorno, estas se ubicaron en los archivos .env tanto del peer como del servidor central según correspondieran. 

### Base de datos.
Finalmente, en cuanto a bases de datos, no fueron utilizadas para este proyecto debido a su alcance, sino que se utilizó el almacenamiento en local (en la misma máquina donde se ejecuta el código).


## Detalles de la organización del código por carpetas o descripción de algún archivo.
A continuación de se presenta la organización de las carpetas o directorios del reto:

![image](https://github.com/EsteTruji/etrujilloc-st0263/assets/82886890/36491d24-8e4e-4ca9-aa7d-85304d80ceb9)

# 4. Descripción del ambiente de EJECUCIÓN (en producción) lenguaje de programación, librerías, paquetes, etc, con sus números de versiones.
Toda la información relacionada con los **lenguajes de programación**, **librerías**, **paquetes**, y demás, se encuentra ubicada en la sección 3.
## IP o nombres de dominio en nube o en la máquina servidor.
A continuación se listan las IPs correspondientes al estar desplegado en AWS:
- **central-server:** 54.225.135.58.
- **peer-1:** 34.230.125.86.
- **peer-2:** 34.195.56.130.

**Aclaración:** No se usaron nombres de dominio debido al alcance del reto, el cual no requería la utilización de estos.

## Descripción y cómo se configura los parámetros del proyecto (ej: ip, puertos, conexión a bases de datos, variables de ambiente, parámetros, etc)
En primer lugar, para configurar las IPs y puertos, es necesario ingresar a los archivos _docker-compose.yml_ (donde se debe configurar tanto el puerto del servidor central y del peer) y en los archivos _.env_ (donde se debe configurar nuevamente el puerto y la IP del servidor central y el peer). 

En segundo lugar, con relación a las variables de entorno, estas se ubicaron en los archivos .env tanto del peer como del servidor central según correspondieran. 

Finalmente, en cuanto a bases de datos, no fueron utilizadas para este proyecto debido a su alcance, sino que se utilizó el almacenamiento en local (en la misma máquina donde se ejecuta el código).

## Cómo se lanza el servidor.
El servidor central se lanza utilizando el comando ```docker compose up``` estando ubicado en la instancia que le corresponde. 
**Aclaración:** Si antes no se había creado la imagen del servidor central, es necesario correr previamente el comando ```docker compose build```.

## Una mini guía de cómo un usuario utilizaría el software o la aplicación.
A manera de mini guía de usuario es posible utilizar la dada en la sección 3, en la parte de **Cómo se compila y ejecuta.**
Una vez se hayan creado y ejecutado los contenedores en docker el usuario tendra acceso a todos los servicios disponibles en el cliente, a continuación se incluye una imagen del menu principal del CLI:

![image](https://github.com/EsteTruji/etrujilloc-st0263/assets/82886890/62859394-18d8-454a-938f-f3ad6f0f95e7)

Para utilizar cualquier servicio bastara con que que el usuario ingrese por terminal el numero que le corresponde.
Una vez selecionado el servicio, el programa le preguntara por mas informacion adicional en caso de ser requerido (ej. Archivo a descargar).
Una vez se complete la operacion dentro del servicio el programa volvera a su menu principal en la que el ususario podra elegir su propia operación.

# Referencias:

- MissCoding. (2022, 9 mayo). Python gRPC Tutorial - Create a gRPC Client and Server in Python with Various Types of gRPC Calls [Vídeo]. YouTube. https://www.youtube.com/watch?v=WB37L7PjI5k
- «Install Docker Engine on Ubuntu». (2024, 31 enero). Docker Documentation. https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository

**Link de descripción del proyecto:** https://eafit-my.sharepoint.com/:w:/g/personal/emontoya_eafit_edu_co/EV2oSNr67ydEktUmUsajFLYB7nTb7Hv21Vk4TWRBZ9E9-w?e=jpMwDz
