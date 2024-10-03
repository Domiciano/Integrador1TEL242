# Instalación de MySQL con Docker

Guarde el siguiente archivo como docker-compose.yml
```
services:
  db:
    platform: linux/x86_64
    image: mysql:5.7
    restart: always
    environment:
      MYSQL_DATABASE: 'db'
      MYSQL_USER: 'user'
      MYSQL_PASSWORD: 'password'
      MYSQL_ROOT_PASSWORD: 'password'
    ports:
      - '3306:3306'
    expose:
      - '3306'
    volumes:
      - my-db:/var/lib/mysql

volumes:
  my-db:
```

Luego, use el comando
```
docker-compose up -d
```

Con esto tendrá una base de datos local en 127.0.0.1:3306. Su usuario es user, su password es password y la base de datos se llamará db

# Instalación de Postgres con Docker

```
services:
  db:
    platform: linux/x86_64
    command: ["--max_connections=1000"]
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
      - my-db:/var/lib/postgresql/data

volumes:
  my-db:
```
