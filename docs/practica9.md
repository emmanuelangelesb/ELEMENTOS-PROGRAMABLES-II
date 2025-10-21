# 📚 Práctica 9 - Práctica_ESP-NOW_MultiNodo_3Botones_3LEDs

> El programa implementa una red **ESP-NOW** con un nodo central que lee **3 botones** y envía un *struct* a **tres ESP32** identificados por su MAC. También se registra un *callback* para **recibir** datos; según el origen, se **enciende/apaga** el LED correspondiente. Refuerza conceptos de comunicación inalámbrica sin IP, estructuras de datos, definición de peers y manejo de GPIO.

---

## 1) Resumén 📌

- **Equipo / Autor(es):** Jesús Cerezo  
- **Curso / Asignatura:** Elementos Programables II  
- **Fecha:** 21/10/25  

---

## 2) Código ⌨️

```bash
# Librerías requeridas
include <esp_now.h>
include <WiFi.h>

# Direcciones MAC de los 3 pares
uint8_t broadcastAddress[]  = {0x7C, 0x2C, 0x67, 0x55, 0xD6, 0x88};  # Peer A
uint8_t broadcastAddress2[] = {0x7C, 0x2C, 0x67, 0x55, 0xD8, 0xDC};  # Peer B
uint8_t broadcastAddress3[] = {0x7C, 0x2C, 0x67, 0x55, 0xD4, 0xE0};  # Peer C

# Pines (ajusta según tu placa)
int led1 = 10;  # LED A
int led2 = 11;  # LED B
int led3 = 12;  # LED C
int boton1 = 2;
int boton2 = 3;
int boton3 = 4;

# Estructura de datos (ESP-NOW ~250 bytes máx)
typedef struct struct_msj {
  char  a[32];
  int   b;
  float c;
  bool  d;
} struct_msj;

# Instancias para envío/recepción
struct_msj datosEnviados;
struct_msj datosRecibidos;
struct_msj datosEnviados2;
struct_msj datosRecibidos2;
struct_msj datosEnviados3;
struct_msj datosRecibidos3;

# Información de peers
esp_now_peer_info_t peerInfo;
esp_now_peer_info_t peerInfo2;
esp_now_peer_info_t peerInfo3;

# Callback de recepción: decide LED según MAC de origen
void OnDataRecv(const esp_now_recv_info_t *info, const uint8_t *tempData, int tam) {
  memcpy(&datosRecibidos, tempData, sizeof(datosRecibidos));

  # (Opcional) imprimir MAC origen
  char macStr[18];
  snprintf(macStr, sizeof(macStr),
           "%02X:%02X:%02X:%02X:%02X:%02X",
           info->src_addr[0], info->src_addr[1], info->src_addr[2],
           info->src_addr[3], info->src_addr[4], info->src_addr[5]);
  Serial.printf("Datos recibidos de: %s\n", macStr);

  # Mapeo por último byte de la MAC
  if(info->src_addr[5] == 0x88) {              # Peer A
    if (datosRecibidos.b == 1) digitalWrite(led1, HIGH); else digitalWrite(led1, LOW);
  }
  if(info->src_addr[5] == 0xE0) {              # Peer C
    if (datosRecibidos.b == 1) digitalWrite(led2, HIGH); else digitalWrite(led2, LOW);
  }
  if(info->src_addr[5] == 0xDC) {              # Peer B
    if (datosRecibidos.b == 1) digitalWrite(led3, HIGH); else digitalWrite(led3, LOW);
  }

  Serial.printf("Int: %d\n", datosRecibidos.b);
}

# Configuración
void setup() {
  Serial.begin(115200);
  WiFi.mode(WIFI_STA);   # Requerido por ESP-NOW

  pinMode(boton1, INPUT);
  pinMode(boton2, INPUT);
  pinMode(boton3, INPUT);
  pinMode(led1, OUTPUT);
  pinMode(led2, OUTPUT);
  pinMode(led3, OUTPUT);

  if (esp_now_init() != ESP_OK) {
    Serial.println("Error inicializando ESP-NOW");
    return;
  }

  # Peer 1
  memset(&peerInfo, 0, sizeof(peerInfo));
  memcpy(peerInfo.peer_addr, broadcastAddress, 6);
  peerInfo.channel = 0;
  peerInfo.encrypt = false;
  if (esp_now_add_peer(&peerInfo) != ESP_OK) { Serial.println("No se pudo agregar el peer"); return; }

  # Peer 2
  memset(&peerInfo2, 0, sizeof(peerInfo2));
  memcpy(peerInfo2.peer_addr, broadcastAddress2, 6);
  peerInfo2.channel = 0;
  peerInfo2.encrypt = false;
  if (esp_now_add_peer(&peerInfo2) != ESP_OK) { Serial.println("No se pudo agregar el peer"); return; }

  # Peer 3
  memset(&peerInfo3, 0, sizeof(peerInfo3));
  memcpy(peerInfo3.peer_addr, broadcastAddress3, 6);
  peerInfo3.channel = 0;
  peerInfo3.encrypt = false;
  if (esp_now_add_peer(&peerInfo3) != ESP_OK) { Serial.println("No se pudo agregar el peer"); return; }

  Serial.println("ESP-NOW listo para enviar");
  esp_now_register_recv_cb(OnDataRecv);
}

# Bucle principal: lee botones y envía a cada peer
void loop() {
  # Log de botones
  Serial.print("Botón 1: "); Serial.print(digitalRead(boton1));
  Serial.print(" | Botón 2: "); Serial.print(digitalRead(boton2));
  Serial.print(" | Botón 3: "); Serial.println(digitalRead(boton3));

  # Payload común
  strcpy(datosEnviados.a, "Hola Mundo");
  datosEnviados.c = 3.14;
  datosEnviados.d = true;

  # Botón 1 -> Peer A
  datosEnviados.b = (digitalRead(boton1) == HIGH) ? 1 : 0;
  esp_err_t result1 = esp_now_send(broadcastAddress,  (uint8_t*)&datosEnviados, sizeof(datosEnviados));

  # Botón 2 -> Peer B
  datosEnviados.b = (digitalRead(boton2) == HIGH) ? 1 : 0;
  esp_err_t result2 = esp_now_send(broadcastAddress2, (uint8_t*)&datosEnviados, sizeof(datosEnviados));

  # Botón 3 -> Peer C
  datosEnviados.b = (digitalRead(boton3) == HIGH) ? 1 : 0;
  esp_err_t result3 = esp_now_send(broadcastAddress3, (uint8_t*)&datosEnviados, sizeof(datosEnviados));

  delay(2000);
}
```