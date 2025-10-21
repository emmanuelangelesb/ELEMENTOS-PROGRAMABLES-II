# 📚PRACTICA 7 - Servidor_Web_LED_Control


---

## 1) Resumen

 
- **Equipo / Autor(es):** _ANGELES BARRETO EMMANUEL, CEREZO PONCE JESUS ALFREDO_  
- **Curso / Asignatura:** _ELEMENTOS PROGRAMABLES II_   
- **Descripción breve:** _El programa convierte el ESP32 en un servidor web local, permitiendo encender y apagar LEDs desde una página HTML alojada en el propio microcontrolador._


---

## 2) Objetivos

- **General:** _ Implementar un servidor web en el ESP32 que permita el **control remoto de LEDs** mediante una interfaz HTML, comprendiendo el funcionamiento del protocolo HTTP y la comunicación entre cliente-servidor embebido._
- **Específicos:**
  - _Diseñar una página web básica alojada en el ESP32 con botones para encender y apagar LEDs._  
  - _Programar las rutas /on y /off para controlar las salidas digitales del microcontrolador._  
  - _Comprender el uso de las librerías WiFi.h y WebServer.h para crear un servidor local funcional._  
  - _Evaluar la respuesta del sistema en tiempo real al interactuar desde un navegador web._

## 3) Alcance y Exclusiones

- **Incluye:** _El desarrollo de un servidor web funcional en el ESP32 capaz de encender y apagar múltiples LEDs a través de una página HTML alojada en el propio microcontrolador, mostrando en tiempo real el estado de cada salida digital._
- **No incluye:** _El control mediante dispositivos externos (como smartphones vía Bluetooth o MQTT), el almacenamiento de datos en bases externas, ni la integración con servicios en la nube._

---

## 4) Requisitos

**Software**
- _Sistema Operativo compatible: Windows, Linux o macOS._  
- _Arduino IDE versión 2.0 o superior._  
- _Extensión de tarjetas ESP32 instalada desde el Gestor de tarjetas._  
- _Librerías necesarias: WiFi.h y WebServer.h._  

**Hardware**
- _Microcontrolador ESP32 DevKit v1._  
- _3 LEDs individuales y 3 resistencias de 220Ω._  
- _Cables Dupont y protoboard._  
- _Fuente de poder o conexión USB para el ESP32._  

**Conocimientos previos**
- _Programación básica en Arduino/C++._  
- _Conceptos de electrónica digital (entrada/salida digital, resistencias, LEDs)._  
- _Conocimiento básico del protocolo HTTP y estructura HTML._  
- _Uso de Git/GitHub para documentación y control de versiones._ 

---

## 5) Instalación

```bash
# ========================================
# LIBRERÍAS NECESARIAS
# ========================================
include <WiFi.h>
include <WebServer.h>

# ========================================
# CONFIGURACIÓN DE RED Y PINES
# ========================================
const char* ssid = "iPhone";
const char* password = "karennajera";

WebServer server(80);

const int ledPin = 20;   # LED principal
const int ledPin1 = 19;  # LED secundario
const int ledPin2 = 21;  # LED terciario

String ledState = "OFF";
String ledState1 = "OFF";
String ledState2 = "OFF";

# ========================================
# FUNCIÓN BASE (Página principal)
# ========================================
void handleRoot() {
  String html = "<!DOCTYPE html><html><head><meta charset='UTF-8'><title>ESP32 LED Control</title></head><body>";
  html += "<h1>ESP32 LED Control</h1>";

  html += "<p>LED 1 is " + ledState + "</p>";
  if (ledState == "OFF")
    html += "<a href=\"/on\"><button>Turn On LED 1</button></a>";
  else
    html += "<a href=\"/off\"><button>Turn Off LED 1</button></a>";

  html += "<p>LED 2 is " + ledState1 + "</p>";
  if (ledState1 == "OFF")
    html += "<a href=\"/on1\"><button>Turn On LED 2</button></a>";
  else
    html += "<a href=\"/off1\"><button>Turn Off LED 2</button></a>";

  html += "<p>LED 3 is " + ledState2 + "</p>";
  if (ledState2 == "OFF")
    html += "<a href=\"/on2\"><button>Turn On LED 3</button></a>";
  else
    html += "<a href=\"/off2\"><button>Turn Off LED 3</button></a>";

  html += "</body></html>";

  server.send(200, "text/html", html);
}

# ========================================
# FUNCIONES DE CONTROL DE LEDS
# ========================================
void handleOn() {
  digitalWrite(ledPin, HIGH);
  ledState = "ON";
  handleRoot();
}

void handleOff() {
  digitalWrite(ledPin, LOW);
  ledState = "OFF";
  handleRoot();
}

void handleOn1() {
  digitalWrite(ledPin1, HIGH);
  ledState1 = "ON";
  handleRoot();
}

void handleOff1() {
  digitalWrite(ledPin1, LOW);
  ledState1 = "OFF";
  handleRoot();
}

void handleOn2() {
  digitalWrite(ledPin2, HIGH);
  ledState2 = "ON";
  handleRoot();
}

void handleOff2() {
  digitalWrite(ledPin2, LOW);
  ledState2 = "OFF";
  handleRoot();
}

# ========================================
# CONFIGURACIÓN INICIAL (setup)
# ========================================
void setup() {
  Serial.begin(115200);

  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW);
  pinMode(ledPin1, OUTPUT);
  digitalWrite(ledPin1, LOW);
  pinMode(ledPin2, OUTPUT);
  digitalWrite(ledPin2, LOW);

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi connected");
  Serial.println(WiFi.localIP());

  server.on("/", handleRoot);
  server.on("/on", handleOn);
  server.on("/off", handleOff);
  server.on("/on1", handleOn1);
  server.on("/off1", handleOff1);
  server.on("/on2", handleOn2);
  server.on("/off2", handleOff2);

  server.begin();
  Serial.println("Server started");
}

# ========================================
# BUCLE PRINCIPAL (loop)
# ========================================
void loop() {
  server.handleClient();
}
```
>En esta práctica se logró implementar un **servidor web embebido en el ESP32**, permitiendo el **control remoto de tres LEDs** desde una página HTML.  
> Se comprendió el uso de las librerías **WiFi.h** y **WebServer.h**, así como la creación de rutas específicas para ejecutar funciones dentro del microcontrolador.  
> Este proyecto demuestra cómo el ESP32 puede actuar como un **microservidor autónomo**, aplicando conceptos de **comunicación cliente-servidor**, **HTML dinámico** y **control digital en tiempo real**, siendo una base sólida para futuros desarrollos en **domótica e IoT**.  

## Evidencias del resultado final 📸

![Diagrama del sistema](recursos/imgs/practica7.jpeg)