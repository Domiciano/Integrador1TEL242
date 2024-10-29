# 1. Instalar dependencias

```xml
<dependency>
  <groupId>io.jsonwebtoken</groupId>
  <artifactId>jjwt</artifactId>
  <version>0.9.1</version>
</dependency>
```

# 2. Hash de password
Evite almacenar la contraseña visible. Para esto haga un hash de la contraseña. Se utilizará SHA-256.


```java
import java.security.MessageDigest;
import java.security.NoSuchAlgorithmException;

public class PasswordHasher {

    public static String hashPassword(String password) {
        try {
            MessageDigest digest = MessageDigest.getInstance("SHA-256");
            byte[] hashedBytes = digest.digest(password.getBytes());
            StringBuilder hexString = new StringBuilder();
            for (byte b : hashedBytes) {
                String hex = Integer.toHexString(0xff & b);
                if (hex.length() == 1) hexString.append('0');
                hexString.append(hex);
            }
            return hexString.toString();
        } catch (NoSuchAlgorithmException e) {
            throw new RuntimeException(e);
        }
    }
}
```

# 3. Almacenar el usuario
Ya puede almacenar el usuario usando la utilidad de hashing

```java 
@PostMapping
public ResponseEntity<?> createUser(@RequestBody User user) {
  var hashedPassword = PasswordHasher.hashPassword(user.getPassword());
  user.setPassword(hashedPassword);
  repository.save(user);
  return ResponseEntity.ok().body(
    Map.of("message", "Successfully created")
  );
}
```

# 4. Utilidad de JWT
Requerirá un generador de token para que los usuarios obtengan uno para poder hacer peticiones a los endpoints que requieren autorización
```java
import io.jsonwebtoken.*;
import java.util.Date;

public class JwtUtil {

    private static final String SECRET_KEY = "Un1v3rs1d4d1c3s1";
    private static final long ACCESS_TOKEN_VALIDITY = 1000 * 60 * 60 * 2; // 2 horas

    public static String generateToken(String email) {
        return Jwts.builder()
                .setSubject(email)
                .setIssuedAt(new Date())
                .setExpiration(new Date(System.currentTimeMillis() + ACCESS_TOKEN_VALIDITY))
                .signWith(SignatureAlgorithm.HS256, SECRET_KEY)
                .compact();
    }

    public static void validateToken(String token) {
        Jwts.parser().setSigningKey(SECRET_KEY).parseClaimsJws(token);
    }
}
```

# 5. Endpoint para obtener token
El endpoint donde se obtiene el token es el mismo login
```java
@PostMapping("login")
public ResponseEntity<?> login(@RequestBody User user) {
  User dbUser = repository.findUserByEmailAndPassword(user.getEmail(), PasswordHasher.hashPassword(user.getPassword()))
                .orElseThrow(() -> new RuntimeException("Usuario no encontrado"));

  if (dbUser.getPassword().equals(PasswordHasher.hashPassword(user.getPassword()))) {
    String accessToken = JwtUtil.generateToken(dbUser.getEmail());
    return ResponseEntity.ok(Map.of("access_token", token));
  } else {
    return ResponseEntity.status(HttpStatus.UNAUTHORIZED).build();
  }
}
```
# 6. Peticiones que requieran login
Cada vez que requiera que un endpoint requiera que quien hace el request tenga la condición de autenticado, deberá pedir en el header la autorización.
El token se deberá presentar como "Bearer <ACCESS_TOKEN>"

```java
@GetMapping
public ResponseEntity<?> list(@RequestHeader("Authorization") String authorization) {
  if (!authorization.startsWith("Bearer ")) {
    return ResponseEntity.status(401).body("Unauthorized");
  }
  try {
    JwtUtil.validateToken(authorization.replace("Bearer ",""));
    return ResponseEntity.ok().body(repository.findAll());
  }catch (Exception ex){
    return ResponseEntity.status(401).body("Unauthorized: "+ex.getLocalizedMessage());
  }
}
```
