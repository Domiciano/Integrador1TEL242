# Implementación en Springboot

Dependencias necesarias para los datos
```
<!--Conector de MySQL-->
<dependency>
   <groupId>mysql</groupId>
   <artifactId>mysql-connector-java</artifactId>
   <version>8.0.26</version>
</dependency>

<!--Conector de Postgres-->
<dependency>
   <groupId>org.postgresql</groupId>
   <artifactId>postgresql</artifactId>
   <version>42.7.4</version>
</dependency>

<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```
No olvide la dependencia Web si es que no la tiene en su lista
```
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```


## Propiedades
Agregue a src/main/resources un archivo llamado application.properties


```
spring.jpa.hibernate.ddl-auto=update
spring.datasource.username=USER
spring.datasource.password=PASS
spring.datasource.tomcat.max-active=10
```

Para MySQL debe agregar la propiedad
```
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
spring.datasource.url=jdbc:mysql://localhost:3306/db
```

Para Postgres debe agregar
```
spring.datasource.driver-class-name=org.postgresql.Driver
spring.datasource.url=jdbc:postgresql://localhost:5432/db
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

Luego, puede usar el campo JSONB y lo debe desearializar como Map


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



