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
spring.datasource.url=jdbc:mysql://200.3.193.22:3306/DATABASE
spring.datasource.username=USER
spring.datasource.password=PASS
spring.datasource.tomcat.max-active=10
```

Para MySQL debe agregar la propiedad
```
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

Para Postgres debe agregar
```
spring.datasource.driver-class-name=org.postgresql.Driver
```



