# Creación del contenedor de Springboot

## 1. Preparación
Este comando le permitirá generar el JAR para ejecutar la aplicación
```
mvn clean package -DskipTests
``` 
Se genera el .jar, luego se debe hacer el dockerfile


## 2. Generación de imagen

```
# Utiliza una imagen base de Java
FROM amazoncorretto:22-jdk

# Establece el directorio de trabajo en la imagen
WORKDIR /app

# Copia el archivo JAR del backend al directorio de trabajo
COPY ./target/artifact.jar /app/backend.jar

# Expone el puerto en el que se ejecutará el backend (ajusta el número de puerto según tus necesidades)
EXPOSE 8080

# Comando para ejecutar la aplicación Spring Boot
CMD ["java", "-jar", "backend.jar"]

```
Luego se hace el build de la imagen de docker
```
docker build -t back:0.0.1 .
```
Todas las versiones vienen con un nombre de imagen y un número de versión. Se puede usar un formato de tres puntos de versión.</br></br>

## 3. Ejecutar imagen
Para ejecutar la imagen en un contenedor use
```
docker run -p 8080:8080 back:0.0.1
```
Esto generará un contenedor con nombre aleatorio que se ejecutará en el puerto 8080 y que está mapeado al puerto 8080 del contenedor.</br></br>

## 4. Publicación
Querrá usar su imagen de docker, para hacerlo debe subir su imagen a DockerHUB. Para iniciar el proceso use un tag
```
docker tag back:0.0.1 domi0620/back:0.0.1
```
En el tag especifique el nombre de su imagen local y luego un nombre cuyo prefijo sea su nombre de usuario en dockerhub.</br></br>
Ahora puede hacer push del stack para tener un backup online de su imagen. Esto le permitirá usar las imágenes y construir un stack de servicios
```
docker push domi0620/back:0.0.1 
```

## 5. Uso de variables de entorno
Puede ser conveniente usar variables de entorno en los proyecto para evitar que se realicen builds consecutivos.
Por ejemplo, en el application.properties del backend puede usar variables de entorno
```
spring.application.name=IntregradorAPI
spring.jpa.hibernate.ddl-auto=update
spring.datasource.username=${DB_USER}
spring.datasource.password=${DB_PASS}
spring.datasource.tomcat.max-active=10
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=${DB_URL}
server.servlet.context-path=${APP_PATH}
```

El llamado ${MI_VARIABLE} lo hace directamente el runtime y permite parametrizar configuraciones o constantes en los programas

## 6. Montaje de docker-compose
En este docker compose sólo se va a hacer el montaje de la base de datos y del Rest API

```
version: "3.7"
services:
  db:
    platform: linux/x86_64
    image: postgres:17
    restart: always
    environment:
      POSTGRES_DB: 'db'
      POSTGRES_USER: 'user'
      POSTGRES_PASSWORD: 'password'
    ports:
      - '5432:5432'
    expose:
      - '5432'
    volumes:
      - projectdata:/var/lib/postgresql/data
    networks:
      - mired

  backend:
    depends_on:
      - db
    image: domi0620/testapi:0.0.2
    restart: always
    ports:
      - '8080:8080'
    expose:
      - '8080'
    environment:
      - DB_URL=jdbc:postgresql://db:5432/db
      - APP_PATH=/introtelapi
      - DB_USER=user
      - DB_PASS=password
    networks:
      - mired

volumes:
  projectdata:
  
networks:
  mired:
```
Para inciar la ejecución use
```
docker-compose up -d
```

Para detener su stack
```
docker-compose down
```

```
docker network ls
```

```
docker network inspect <NETWORK ID>
```

Si quiere tener un administrador web para la base de datos, añada el servicio
```
  pgadmin:
    image: dpage/pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@example.com
      PGADMIN_DEFAULT_PASSWORD: securepassword
    ports:
      - "5050:80"
    depends_on:
      - db
```

# Frontend

## 1. Preparación
Configuración del servidor nginx. Corresponde al archivo nginx.conf. Este archivo se deberá copiar al contenedor de NGINX
```
server {
    listen 3000;
    root /usr/share/nginx/html;
    index index.html;
    location / {
        try_files $uri $uri/ =404;
    }
}
```

## 2. Generación de imagen

```
# Utiliza una imagen base con Nginx instalado
FROM --platform=linux/amd64 nginx:latest
COPY nginx.conf /etc/nginx/conf.d/default.conf
COPY . /usr/share/nginx/html/
# Exponer el puerto 80 para permitir el acceso a través de HTTP
EXPOSE 3000
```

```
docker build -t front:0.0.1 .
```

## 3. Ejecutar imagen
Para ejecutar la imagen en un contenedor use
```
docker run -p 3000:3000 front:0.0.1
```
Esto generará un contenedor con nombre aleatorio que se ejecutará en el puerto 8080 y que está mapeado al puerto 8080 del contenedor.</br></br>

## 4. Publicación
Querrá usar su imagen de docker, para hacerlo debe subir su imagen a DockerHUB. Para iniciar el proceso use un tag
```
docker tag front:0.0.1 domi0620/front:0.0.1
```
En el tag especifique el nombre de su imagen local y luego un nombre cuyo prefijo sea su nombre de usuario en dockerhub.</br></br>

Ahora puede hacer push del stack para tener un backup online de su imagen. Esto le permitirá usar las imágenes y construir un stack de servicios
```
docker push domi0620/front:0.0.1          
```

# Despliegue de proyectos de Python

Primero en su proyecto, exporte la lista de dependencias requeridas para que el contenedor instale
```
pip freeze > requirements.txt
```
Tenga un sistema de archivos similar a este
```
project/
├── app/
│   ├── main.py           # Archivo principal de FastAPI
│   ├── requirements.txt  # Dependencias de Python
└── Dockerfile            # Dockerfile para crear la imagen
```

Puede usar un Dockerfile similar a
```
FROM python:3.10-slim
WORKDIR /app
COPY ./app .
RUN apt-get update -y && apt-get install -y gcc
RUN pip install --no-cache-dir -r requirements.txt
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```
Si requiere que no se copien archivos o carpetas como pycaché, cree un archivo llamado .dockerignore con el siguiente contenido
```
__pycache__/
```

Cuando inserta variables de entorno, por ejemplo en un docker-compose.yml, puede usar la variable

```
import os
...
my_var = os.getenv("MY_VAR")

```



# Depliegue en el servidor de Icesi

## 1. Desplegar a producción
Para subir en un orquestador de contenedores como portainer se debe usar la configuración establecida por el administrador para poder hacer la publicación.

```
version: "3.7"
services:
  bannerdb:
    command: ["--max_connections=1000"]
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: 'db'
      MYSQL_USER: 'user'
      MYSQL_PASSWORD: 'password'
      MYSQL_ROOT_PASSWORD: 'password'
    volumes:
      - bannerdata:/var/lib/mysql
    networks:
      - proxy

  bannerbackend:
    depends_on:
      - bannerdb
    image: domi0620/exampleapp:0.0.3
    restart: always

    environment:
      - DATA_SOURCE_URL=jdbc:mysql://bannerdb:3306/db
      - APP_PATH=/introtel
    networks:
      - proxy
    deploy:
        replicas: 1
        labels: 
          com.df.distribute: "false"
          com.df.notify: "true"
          com.df.port: 8080
          com.df.servicePath: "/introtel"
          


volumes:
  bannerdata:
    external: true

networks:
  proxy:
    external: true
```
