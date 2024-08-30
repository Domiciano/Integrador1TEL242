```java
public class Main {
    public static void main(String[] args) throws IOException {

        var interfaces = NetworkInterface.getNetworkInterfaces();
        while(interfaces.hasMoreElements()) {
            var interfaz = interfaces.nextElement();
            System.out.println(interfaz.getDisplayName());
            interfaz.inetAddresses().forEach(inetAddress -> {
                System.out.println(inetAddress.getHostAddress());
            });
        }

        InetAddress address = InetAddress.getByName("www.icesi.edu.co");
        System.out.println(address.getHostAddress());
    }
}
```
