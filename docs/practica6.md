// Código Arduino (copiable)
#include <WiFi.h>
#include <WebServer.h>

// Credenciales de red
const char* ssid = "iPhone";
const char* password = "karennajera";

// Servidor web en puerto 80
WebServer server(80);

// GPIOs para 3 LEDs
const int ledPin  = 20;  // LED0 (ajusta a tu placa)
const int ledPin1 = 19;  // LED1
const int ledPin2 = 21;  // LED2

// Estado textual
String ledState  = "OFF";
String ledState1 = "OFF";
String ledState2 = "OFF";

// Página raíz
void handleRoot() {
  String html =
    "<!DOCTYPE html><html><head><meta charset='UTF-8'>"
    "<meta name='viewport' content='width=device-width, initial-scale=1'>"
    "<title>ESP32 LED Control</title>"
    "<style>body{font-family:system-ui;max-width:680px;margin:0 auto;padding:24px;"
    "background:#0b0d13;color:#e6edf3}"
    "a button{padding:8px 12px;border-radius:10px;border:1px solid rgba(255,255,255,.2);"
    "background:rgba(255,255,255,.06);color:#e6edf3;cursor:pointer;margin-right:8px;margin-bottom:8px}</style>"
    "</head><body>";

  html += "<h1>ESP32 LED Control</h1>";
  html += "<p>LED0: " + ledState + " | LED1: " + ledState1 + " | LED2: " + ledState2 + "</p>";

  // LED0
  html += (ledState == "OFF")
            ? "<a href=\"/on\"><button>Encender LED0</button></a>"
            : "<a href=\"/off\"><button>Apagar LED0</button></a>";
  // LED1
  html += (ledState1 == "OFF")
            ? "<a href=\"/on1\"><button>Encender LED1</button></a>"
            : "<a href=\"/off1\"><button>Apagar LED1</button></a>";
  // LED2
  html += (ledState2 == "OFF")
            ? "<a href=\"/on2\"><button>Encender LED2</button></a>"
            : "<a href=\"/off2\"><button>Apagar LED2</button></a>";

  html += "</body></html>";
  server.send(200, "text/html", html);
}

// Handlers encendido/apagado
void handleOn()   { digitalWrite(ledPin,  HIGH); ledState  = "ON";  handleRoot(); }
void handleOff()  { digitalWrite(ledPin,  LOW);  ledState  = "OFF"; handleRoot(); }
void handleOn1()  { digitalWrite(ledPin1, HIGH); ledState1 = "ON";  handleRoot(); }
void handleOff1() { digitalWrite(ledPin1, LOW);  ledState1 = "OFF"; handleRoot(); }
void handleOn2()  { digitalWrite(ledPin2, HIGH); ledState2 = "ON";  handleRoot(); }
void handleOff2() { digitalWrite(ledPin2, LOW);  ledState2 = "OFF"; handleRoot(); }

void setup() {
  Serial.begin(115200);

  pinMode(ledPin,  OUTPUT); digitalWrite(ledPin,  LOW);
  pinMode(ledPin1, OUTPUT); digitalWrite(ledPin1, LOW);
  pinMode(ledPin2, OUTPUT); digitalWrite(ledPin2, LOW);

  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) { delay(500); Serial.print("."); }
  Serial.println("\nWiFi connected");
  Serial.println(WiFi.localIP()); // Abre http://<IP>/

  // Rutas
  server.on("/",   handleRoot);
  server.on("/on", handleOn);
  server.on("/off",handleOff);
  server.on("/on1",handleOn1);
  server.on("/off1",handleOff1);
  server.on("/on2",handleOn2);
  server.on("/off2",handleOff2);

  server.begin();
  Serial.println("Server started");
}

void loop() {
  server.handleClient();
}
