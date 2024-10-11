```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
</head>
<body>
    
    <form id="userForm">
        <div class="mb-3">
            <input id="nameInput" class="form-control" type="text" placeholder="Nombre completo">
        </div>
        <div class="mb-3">
            <input id="natIDInput" class="form-control" type="text" placeholder="Documento">
        </div>
        <div class="mb-3">
            <input id="emailInput" class="form-control" type="email" placeholder="Correo electrónico">
        </div>
        <div class="mb-3">
            <input id="passwordInput" class="form-control" type="password" placeholder="Password">
        </div>
        <button class="btn btn-primary" id="signupBtn">Registrar</button>
    </form>

</body>
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
<script src="registro.js"></script>
</html>
```


``` javascript
const nameInput = document.getElementById('nameInput');
const natIDInput = document.getElementById('natIDInput');
const emailInput = document.getElementById('emailInput');
const passwordInput = document.getElementById('passwordInput');
const signupBtn = document.getElementById('signupBtn');


const postUser = async (json) => {
    let response = await fetch('http://localhost:8080/users/create', {
        method: 'POST',
        body: json,
        headers:{'Content-Type':'application/json'}
    });
    let responseJSON = await response.json();
    console.log(responseJSON);
}

signupBtn.addEventListener('click', (event)=>{
    event.preventDefault();

    let name = nameInput.value;
    let natID = natIDInput.value;
    let email = emailInput.value;
    let password = passwordInput.value;
    
    let user = {
        name: name,
        natID: natID,
        email: email,
        password: password
    };

    //Obj -> String
    let json = JSON.stringify(user);
    console.log(json);

    //POST
    postUser(json);
    console.log('Fin del metodo');
});
```

```java
package com.example.intregradorapi.controller;

import com.example.intregradorapi.entity.User;
import com.example.intregradorapi.repository.UserRepository;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.util.Optional;

@RestController
@CrossOrigin(maxAge = 3600)
public class UserController {

    @Autowired
    private UserRepository userRepository;

    @PostMapping("users/create")
    public ResponseEntity<?> createUser(@RequestBody User user) {
        userRepository.save(user);
        return ResponseEntity.status(200).body(user);
    }

    @GetMapping("users/list")
    public ResponseEntity<?> listUsers() {
        var users = userRepository.findAll();
        return ResponseEntity.status(200).body(users);
    }

    //users?email=domic.rincon@gmail.com -> Request
    //users/domic.rincon@gmail.com -> Path Variable
    @GetMapping("users")
    public ResponseEntity<?> getUserByEmail(@RequestParam("email") String email){
        Optional<User> optuser = userRepository.findUserByEmail(email);
        if(optuser.isPresent()){
            var user = optuser.get();
            return ResponseEntity.status(200).body(user);
        }else{
            return ResponseEntity.status(404).body("User not found");
        }
    }



    //users/list
    //users/delete
    //users/login

}
```
Repository
```
package com.example.intregradorapi.repository;

import com.example.intregradorapi.entity.User;
import org.springframework.data.repository.CrudRepository;

import java.util.Optional;

//CRUD para entidad
//C: create, R: read, U:update, D:delete
public interface UserRepository extends CrudRepository<User, Long> {
    Optional<User> findUserByEmail(String email);
}
```

