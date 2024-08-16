# Cliente
```java
import java.io.IOException;
import java.net.Socket;
import java.nio.charset.StandardCharsets;
import java.util.Scanner;

public class Client {

    public static void main(String[] args) throws IOException {
        //Estoy enviando la solicitud de conexion
        Socket socket = new Socket("8.tcp.ngrok.io", 12561);

        //Lectura
        new Thread(
                () -> {
                    while (true) {
                        try {
                            var is = socket.getInputStream();
                            byte[] buffer = new byte[1024];
                            is.read(buffer);
                            String message = new String(buffer).trim();
                            System.out.println("Recibido> " + message);
                        } catch (IOException ex) {
                            ex.printStackTrace();
                        }
                    }
                }
        ).start();


        //Escritura
        Scanner scanner = new Scanner(System.in);
        while (true) {
            String line = scanner.nextLine();
            socket.getOutputStream().write(line.getBytes());
        }
    }

}
```

# Server
```java
import java.io.IOException;
import java.net.ServerSocket;
import java.net.Socket;
import java.util.ArrayList;

public class Server {

    static ArrayList<Session> sessions = new ArrayList<>();

    public static void main(String[] args) throws IOException {
        ServerSocket server = new ServerSocket(6000);
        while(true) {
            System.out.println("Esperando conexión...");
            Socket socket = server.accept();
            System.out.println("Conexión aceptada");

            System.out.println(socket.getPort());
            Session session = new Session(socket);
            session.startReceivingProcess();
            sessions.add(session);
        }
    }

}
```

# Session
```java
import java.io.IOException;
import java.net.Socket;

public class Session {

    private Socket socket;

    public Session(Socket socket){
        this.socket = socket;
    }

    public void startReceivingProcess(){
        //Lectura
        new Thread(
                () -> {
                    while (true) {
                        try {
                            var is = socket.getInputStream();
                            byte[] buffer = new byte[1024];
                            is.read(buffer);
                            String message = new String(buffer).trim();
                            System.out.println("Recibido> " + message);
                            sendBroadcast(message);
                        } catch (IOException ex) {
                            ex.printStackTrace();
                        }
                    }
                }
        ).start();
    }

    private void sendBroadcast(String message) {
        Server.sessions.forEach(session -> {
            session.sendMessage(message);
        });
    }

    public void sendMessage(String message){
        new Thread(
                ()->{
                    try {
                        socket.getOutputStream().write(message.getBytes());
                    } catch (IOException e) {
                        throw new RuntimeException(e);
                    }
                }
        ).start();

    }

}
```

