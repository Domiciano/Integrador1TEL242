## GET Request
```c++
#include <Arduino.h>
#include <WiFi.h>
#include <HTTPClient.h>


const char* ssid = "PUBLICA";
const char* password = "";


String url = "https://fakestoreapi.com/products";


void initWiFi() {
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi ..");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print('.');
    delay(1000);
  }
  Serial.println("Connected!!");
  Serial.println(WiFi.localIP());
  
}

void setup() {
  Serial.begin(115200);
  Serial.println("Inicializando");
  initWiFi();
  HTTPClient http;
  http.begin(url.c_str());
  int httpResponseCode = http.GET();

  if (httpResponseCode > 0) {
    Serial.print("HTTP Response code: ");
    Serial.println(httpResponseCode);
    String payload = http.getString();
    Serial.println(payload);
  } else {
    Serial.print("Error code: ");
    Serial.println(httpResponseCode);
  }
  http.end();
}

void loop() {
  
}
```

## POST Request

```c++
#include <Arduino.h>
#include <WiFi.h>
#include <HTTPClient.h>


const char* ssid = "PUBLICA";
const char* password = "";


String url = "https://facelogprueba.firebaseio.com/db.json";

void initWiFi() {
  WiFi.mode(WIFI_STA);
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi ..");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print('.');
    delay(1000);
  }
  Serial.println("Connected!!");
  Serial.println(WiFi.localIP());
  
}

void setup() {
  Serial.begin(9600);
  initWiFi();
  HTTPClient http;
  http.begin(url.c_str());
  http.addHeader("Content-Type", "application/json");
  //ENVIO DE DATOS
  int httpResponseCode = http.POST("{\"api_key\":\"tPmAT5Ab3j7F9\",\"sensor\":\"BME280\",\"value1\":\"24.25\",\"value2\":\"49.54\",\"value3\":\"1005.14\"}");


  //RECEPCIÓN
  if (httpResponseCode > 0) {
    Serial.print("HTTP Response code: ");
    Serial.println(httpResponseCode);
    String payload = http.getString();
    Serial.println(payload);
  } else {
    Serial.print("Error code: ");
    Serial.println(httpResponseCode);
  }

  http.end();
}

void loop() {
  
}
```
## Decodificación JSON
Debe instalar la librería Arduino_JSON<br>
https://github.com/arduino-libraries/Arduino_JSON
```c++
#include <Arduino.h>
#include <WiFi.h>
#include <HTTPClient.h>
#include <Arduino_JSON.h>



const char* ssid = "PUBLICA";
const char* password = "";


String url = "https://facelogprueba.firebaseio.com/pokemons.json";


void setup() {
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);
  initWiFi();
  HTTPClient http;
  http.begin(url.c_str());
  http.addHeader("Content-Type", "application/json");
  //ENVIO DE DATOS
  int httpResponseCode = http.POST("{\"api_key\":\"tPmAT5Ab3j7F9\",\"sensor\":\"BME280\",\"value1\":\"24.25\",\"value2\":\"49.54\",\"value3\":\"1005.14\"}");


  //RECEPCIÓN
  if (httpResponseCode > 0) {
    Serial.print("HTTP Response code: ");
    Serial.println(httpResponseCode);
    String payload = http.getString();
    Serial.println(payload);

    JSONVar myObject = JSON.parse(payload);
    Serial.println(myObject);

    JSONVar keys = myObject.keys();
    for (int i = 0; i < keys.length(); i++) {
      JSONVar value = myObject[keys[i]];
      Serial.print(keys[i]);
      Serial.print(" = ");
      Serial.println(value);
    }


  } else {
    Serial.print("Error code: ");
    Serial.println(httpResponseCode);
  }

  http.end();
}

void loop() {
  
}

void initWiFi() {
  WiFi.begin(ssid, password);
  Serial.print("Connecting to WiFi ..");
  while (WiFi.status() != WL_CONNECTED) {
    Serial.print('.');
    delay(1000);
  }
  Serial.println("Connected!!");
  Serial.println(WiFi.localIP());
  
}
```

## Codificando en JSON

Debe instalar la librería Arduino_JSON<br>
https://github.com/arduino-libraries/Arduino_JSON

### Creando un objeto simple
```c++
#include <Arduino_JSON.h>

...

JSONVar object;
object["key1"] = "value1";
object["key2"] = "value2";
object["key2"] = "value3";
String jsonString = JSON.stringify(object);
Serial.println(jsonString);
```

### Creando un arreglo

```c++
JSONVar jsonArray;
jsonArray[0] = 20;
jsonArray[1] = 214;
jsonArray[2] = 317;
jsonArray[3] = 498;
...
```

### Componiendo objetos
```c++

JSONVar object;
object["key1"] = "value1";

JSONVar jsonArray;
jsonArray[0] = 142;
jsonArray[1] = 45;
jsonArray[2] = 1020;

object["key2"] = jsonArray;

```


### Creación de clases y objetos en c++
La cabecera de la clase es
```c++
#ifndef ACELEROMETRO_H
#define ACELEROMETRO_H
#include <Arduino.h>

class Acelerometro {
private:
    float x;  
    float y;  
    float z; 

public:
    Acelerometro(float x = 0.0, float y = 0.0, float z = 0.0);
    float getX() const;
    float getY() const;
    float getZ() const;

    void setX(float x);
    void setY(float y);
    void setZ(float z);
    
    float getMagnitud() const;

    String toJSON() const;
};

#endif 
```
La implementación de la clase es
```c++
#include <Arduino.h>
#include "Acelerometro.h"
#include <Arduino_JSON.h>
#include <cmath>  
  

// Constructor
Acelerometro::Acelerometro(float x, float y, float z) : x(x), y(y), z(z) {}

// Métodos para obtener los valores de los ejes
float Acelerometro::getX() const {
    return x;
}

float Acelerometro::getY() const {
    return y;
}

float Acelerometro::getZ() const {
    return z;
}

// Métodos para establecer los valores de los ejes
void Acelerometro::setX(float x) {
    this->x = x;
}

void Acelerometro::setY(float y) {
    this->y = y;
}

void Acelerometro::setZ(float z) {
    this->z = z;
}

// Método para obtener la magnitud de la aceleración
float Acelerometro::getMagnitud() const {
    return std::sqrt(x * x + y * y + z * z);
}

// Método para transformar a JSON
String Acelerometro::toJSON() const {
    JSONVar object;
    object["x"] = this->x;
    object["y"] = this->y;
    object["z"] = this->z;
    String jsonString = JSON.stringify(object);
    return jsonString;
}
```
Y para usar el objeto en cualquier contexto
```c++
#include <Acelerometro.h>
...
Acelerometro acelerometro(1.0, 2.0, 3.0);
```
