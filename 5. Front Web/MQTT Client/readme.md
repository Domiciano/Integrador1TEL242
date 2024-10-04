# MQTT
Puede usar este código base como cliente MQTT

```javascript
const client = new Paho.MQTT.Client('broker.hivemq.com', Number(8000), "webmqttuser1");

//Listener de mensajes
client.onMessageArrived = (msg) => {
    console.log("Arrived!: "+msg.payloadString);
}

//Función para conectarse al broker
client.connect({onSuccess:() => {
    console.log("conectado!")
    client.subscribe("test/101/beta");
}});

//Función para enviar un mensaje a un topic
const sendMessage = () => {
    message = new Paho.MQTT.Message("Hello from JS");
    message.destinationName = "test/101/beta";
    client.send(message);
};
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <link href="css/index.css" rel="stylesheet">
    <title>Integrador TEL</title>
</head>
<body>
    <h1>Integrador 1 telemática ICESI</h1>
    <input type="text" placeholder="Mensaje">
    <button id="okbtn">OK</button>
</body>
</html>
<script src="https://cdnjs.cloudflare.com/ajax/libs/paho-mqtt/1.0.1/mqttws31.js" type="text/javascript"></script>
<script src="index.js"></script>
```


