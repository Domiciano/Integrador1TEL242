# Relaciones de entidades en Springboot


```
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
Finalmente las relaciones de tablas que necesitará usar son: 1 a muchos y muchos a muchos. Supongo que tiene una relación entre las entidades Curso y Profesor. Para configurar la relación usando JPA, las clases se verán así:

```
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
```
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



