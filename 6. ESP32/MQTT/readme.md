## Conexión a MQTT Server SIN SSL

Para usar este código necesitará PubSubClient de Nick O'Leary
https://github.com/knolleary/pubsubclient

```c++
#include <Arduino.h>
#include <WiFi.h>
#include <PubSubClient.h>

// Configuración de la red Wi-Fi
const char* ssid = "PUBLICA";
const char* password = "";

// Configuración del servidor MQTT
const char* mqttServer = "broker.hivemq.com";
const int mqttPort = 1883;
const char* clientName = "ESP32ClienteIcesi001";

// Configuración del topic
const char* topic = "test/101/beta";

// Objeto WiFiClient
WiFiClient wifiClient;

// Objeto PubSubClient
PubSubClient mqttClient(wifiClient);

void callback(char* topic, byte* payload, unsigned int length) {
  // Imprime el mensaje recibido
  Serial.print("Mensaje recibido en el topic ");
  Serial.print(topic);
  Serial.print(": ");
  for (int i = 0; i < length; i++) {
    Serial.print((char)payload[i]);
  }
  Serial.println();
}

void keepAlive(){
  if (!mqttClient.connected()) {
    Serial.println("Reconectando");
    // Intenta conectarse al servidor MQTT
    while (!mqttClient.connected()) {
      Serial.println("Intentando conectar al servidor MQTT...");
      if (mqttClient.connect(clientName)) {
        Serial.println("Conectado al servidor MQTT!");
      } else {
        Serial.print("Error al conectar: ");
        Serial.println(mqttClient.state());
        delay(5000);
      }
    }
    mqttClient.subscribe(topic);
  }
}

void setup() {
  // Inicializa la comunicación serial
  Serial.begin(115200);

  // Conecta a la red Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }

  // Inicializa el cliente MQTT
  mqttClient.setServer(mqttServer, mqttPort);
  mqttClient.setCallback(callback);

  // Intenta conectarse al servidor MQTT
  while (!mqttClient.connected()) {
    Serial.println("Intentando conectar al servidor MQTT...");
    if (mqttClient.connect(clientName)) {
      Serial.println("Conectado al servidor MQTT!");
    } else {
      Serial.print("Error al conectar: ");
      Serial.println(mqttClient.state());
      delay(5000);
    }
  }

  // Suscríbete al topic
  mqttClient.subscribe(topic);
}



void loop() {
  // Procesa los mensajes del servidor MQTT
  mqttClient.loop();
  keepAlive();
}



void serialEvent() {
  if (Serial.available() > 0) {
    String data = Serial.readStringUntil('\n');
    mqttClient.publish("test/101/beta", data.c_str(), 1);
  }
}
```

