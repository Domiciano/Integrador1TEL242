# 1. Instalación de base de datos

## Instalación de MySQL con Docker

Guarde el siguiente archivo como docker-compose.yml
```yml
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
```sh
docker-compose up -d
```

Con esto tendrá una base de datos local en 127.0.0.1:3306. Su usuario es user, su password es password y la base de datos se llamará db

## Instalación de Postgres con Docker

```yml
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



# 3. Relaciones de entidades en Springboot

Usted puede especificar una tabla usando
```java
import jakarta.persistence.*;

@Entity
public class User {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private long id;

    private String name;

    private String email;

    private String password;


    //ToDo: Hacer Getters y Setters
}
```
Donde @GeneratedValue(strategy = GenerationType.AUTO) se usa para indicar que la variable será INT y AUTO_INCREMENT

# Relación 1 a Muchos
Suponga que tiene una relación entre las entidades Curso y Profesor. Para configurar la relación usando JPA, las clases se verán así:

```java
import jakarta.persistence.*;

@Entity
@Table(name = "profesor")
public class Profesor {

    @Id
    @GeneratedValue(strategy= GenerationType.AUTO)
    private long id;

    private String name;

    @OneToMany(mappedBy = "profesor") //Nombre de la propiedad en la otra clase
    private List<Curso> cursos;
    
    //ToDo: Hacer Getters y Setters
}

```
Note que @OneToMany representa el 1 en el diagrama y @ManyToOne representa el *. Además @OneToMany en mappedBy se especifica el nombre del objeto de la clase relacionada, en @ManyToOne en su propiedad name se especifica el nombre del campo de la tabla donde se aloja la llave foránea
```java
import jakarta.persistence.*;

@Entity
@Table(name = "curso")
public class Curso {
    @Id
    @GeneratedValue(strategy= GenerationType.AUTO)
    private long id;

    private String name;

    private String program;

    @ManyToOne
    @JoinColumn(name = "profeID")
    Profesor profesor;

    //ToDo: Hacer Getters y Setters
}
```

# 4. Componentes del Backend

## Controller
Esta capa está encargada de recibir las solicitudes del frontend. Aquí programará los GET, POST, PUT o DELETE.<br>
Tenga en cuenta:<br>
<ol>
    <li>GET. Úselo cuando quiere consultar información a la base de datos</li>
    <li>POST. Úselo cuando quiere agregar un nuevo registro a la base de datos</li>
    <li>PUT. Úselo cuando quiere agregar/reemplazar/modificar un registro en la base de datos</li>
    <li>DELETE. Úselo cuando quiere eliminar información de la base de datos</li>
</ol>
Un controller se ve así:

```java
@RestController
public class EchoController {

    @Autowired
    MyRepository myRepository;

    @GetMapping("echo")
    public String echo(){
        return "echo";
    }
    
    @PostMapping("another")
    public ResponseEntity<?> another(){
        return ResponseEntity.status(200).body("Another");
    }

}
```

## Repository
En un repository, va el CRUD (Create, Read, Update, Delete) de cada una de las entidades. Por ejemplo si tenemos una entidad User, un repository puede verse así

```java
public interface UserRepository extends CrudRepository<User, Long> {

}
``` 

Donde <User, Long> el el tipo de dato de la entidad y el tipo de dato de la llave primaria de esa entidad. La interfaz por defecto tendrá los métodos save(User user), deleteById(Integer id), deleteAll(), findAll(), findById(), entre otros. 

La clase repository es llamada por la clase controller donde se necesite usar.<br><br>

Si requiere ordenamientos y paginación puede usar
```java
public interface UserRepository extends CrudRepository<User, Long> {

}
``` 
## Acciones
Todas las acciones y consultas se hacen por medio del repositorio.<br><br>

### Obtener todos los registros de la tabla 
```java
repository.findAll()
```
### Insertar datos
```java
repository.save(user)
```
Donde user es una instancia de la entidad User

### Obtener registros con filtro de búsqueda
En el repositorio debe escribir la consulta

```java
@Query("SELECT u FROM User u WHERE u.name = :name")
List<User> findUsersByVehicleBrand(@Param("name") String name);
```

Una consulta a través de 2 tablas se ve así
```java
@Query("SELECT u FROM User u JOIN u.vehicleList v WHERE v.brand = :brand")
List<User> findUsersByVehicleBrand(@Param("brand") String brand);
```


# 5. Almacenar JSON en Postgres

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
Luego, puede usar el campo JSONB y lo puede desearializar como Map, como List o como el objeto que usted requiera. Pueden ser instancias de MiObjeto.

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
    private MiObjeto data;

    //GETTERS Y SETTERS
}
```






