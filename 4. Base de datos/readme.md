# 1. Instalación de base de datos

## Instalación de MySQL con Docker

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

## Instalación de Postgres con Docker

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

# 2. Implementación

## Implementación en Springboot
Dependencias necesarias para crear clases que originan tablas en la base de datos
```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```
Conector de MySQL
```xml
<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <version>8.0.26</version>
</dependency>
```
Conector de Postgres
```xml
<dependency>
   <groupId>org.postgresql</groupId>
   <artifactId>postgresql</artifactId>
   <version>42.7.4</version>
</dependency>
```
Por si acaso, no olvide la dependencia Web si es que no la tiene en su lista
```xml
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```


## Propiedades
Agregue a src/main/resources un archivo llamado application.properties
```properties
spring.jpa.hibernate.ddl-auto=update
spring.datasource.username=<USER>
spring.datasource.password=<PASS>
spring.datasource.tomcat.max-active=10
```

Para MySQL debe agregar la propiedad
```properties
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/<DB>
```

Para Postgres debe agregar
```properties
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/<DB>
```

## Almacenar JSONB como valor en Postgres

Lo primero que debe saber es que Hibernate es el ORM lo que permite transformar registros de base de datos en objetos. Para que Hibernate pueda almacenar e interactuar con campos de este tipo debe primero usar la dependencia

```xml
<dependency>
   <groupId>com.vladmihalcea</groupId>
   <artifactId>hibernate-types-60</artifactId>
   <version>2.19.2</version>
</dependency>
```
El 60 representa la versión 6 de Hibernate.<br><br>
Luego, puede usar el campo JSONB y lo puede desearializar como Map o como List

```java
@Entity
@Table(name = "users")
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    private String name;

    private String email;

    private String password;

    @Type(JsonBinaryType.class)  // Usa el tipo JSONB de hibernate-types
    @Column(columnDefinition = "jsonb")
    private Map<String, Object> data;

    //GETTERS Y SETTERS
}
```





