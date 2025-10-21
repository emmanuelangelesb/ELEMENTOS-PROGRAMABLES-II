# 📚 Práctica 7 - Práctica_ControlWeb_3LEDs_ESP32

> El programa convierte al ESP32 en un servidor web que permite **controlar tres LEDs** mediante una interfaz accesible desde cualquier navegador conectado a la misma red Wi-Fi. Cada LED puede encenderse o apagarse a través de rutas HTTP específicas (/on, /off, /on1, /off1, /on2, /off2). Esta práctica enseña los principios de **comunicación cliente-servidor**, **manejo de pines digitales**, y **generación dinámica de contenido HTML**.

---

## 1) Resumén 📌

- **Equipo / Autor(es):** Jesús Cerezo  
- **Curso / Asignatura:** Elementos Programables II  
- **Fecha:** 21/10/25  

---

## 2) Código ⌨️

```bash
# Librerías necesarias para conexión Wi-Fi y servidor web
include <WiFi.h>
include <WebServer.h>
 
# Credenciales de red Wi-Fi
const char* ssid = "iPhone";
const char* password = "karennajera";
 
# Creación del servidor web en el puerto 80
WebServer server(80);
 
# Definición de pines donde se conectan los LEDs
const int ledPin = 20;  # LED 0
const int ledPin1 = 19; # LED 1
const int ledPin2 = 21; # LED 2

# Variables de estado de cada LED
String ledState = "OFF";
String ledState1 = "OFF";
String ledState2 = "OFF";
 
# Función principal que genera la página web
void handleRoot() {
  /*
   La función crea una página HTML con botones que permiten encender y apagar
   los LEDs. Cada botón redirige a una URL específica (/on, /off, /on1, /off1, etc.)
   que activa las funciones correspondientes.
  */
  String html = "<!DOCTYPE html><html><head><meta charset='UTF-8'><title>ESP32 LED Control</title></head><body>";
  html += "<h1>ESP32 LED Control</h1>";
  html += "<p>LED is " + ledState + "</p>";
  if (ledState == "OFF")
    html += "<a href=\"/on\"><button>Turn On</button></a>";
  else
    html += "<a href=\"/off\"><button>Turn Off</button></a>";
  html += "</body></html>";
  server.send(200, "text/html", html);
}
 
# Función para encender LED0
void handleOn() {
  digitalWrite(ledPin, HIGH);
  ledState = "ON";
  handleRoot();  # Refresca la página con el nuevo estado
}

# Función para apagar LED0
void handleOff() {
  digitalWrite(ledPin, LOW);
  ledState = "OFF";
  handleRoot();  # Refresca la página con el nuevo estado
}

# Funciones para LED1
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

# Funciones para LED2
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

# Configuración inicial del sistema
void setup() {
  Serial.begin(115200);
  pinMode(ledPin, OUTPUT);
  digitalWrite(ledPin, LOW);
 
  pinMode(ledPin1, OUTPUT);
  digitalWrite(ledPin1, LOW);
 
  pinMode(ledPin2, OUTPUT);
  digitalWrite(ledPin2, LOW);
 
  # Conexión Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(500);
    Serial.print(".");
  }
  Serial.println("\nWiFi connected");
  Serial.println(WiFi.localIP());
 
  # Definición de rutas del servidor web
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
 
# Bucle principal que mantiene el servidor activo
void loop() {
  server.handleClient();
}
