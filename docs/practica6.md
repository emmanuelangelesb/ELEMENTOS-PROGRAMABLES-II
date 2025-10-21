# 📚PRACTICA 6 - Comunicación wifi

---

## 1) Resumen

- **Equipo / Autor(es):** _ANGELES_BARRETO_EMMANUEL, CEREZO_PONCE_JESUS_ALFREDO_  
- **Curso / Asignatura:** _ELEMENTOS_PROGRAMABLES_II_  
- **Descripción breve:** _El programa configura un ESP32 como dispositivo Bluetooth Low Energy (BLE) que anuncia un servicio y una característica; permite comunicación básica con clientes BLE (smartphone, PC) para lectura/escritura de una cadena de texto._


---

## 2) Objetivos

- **General:** _Implementar la comunicación BLE en el ESP32 para transmitir y recibir datos básicos y comprender la estructura de servicios y características BLE._  
- **Específicos:**  
  - _Inicializar el módulo BLE con un nombre identificable._  
  - _Crear un servicio y una característica con UUIDs personalizados._  
  - _Anunciar (advertise) el servicio para que clientes BLE lo detecten._  
  - _Probar la lectura/escritura de la característica desde un cliente BLE._


## 3) Alcance y Exclusiones

- **Incluye:** _Configuración del ESP32 como servidor BLE, creación de servicio y característica, y advertising para pruebas con apps BLE._  
- **No incluye:** _Conexiones múltiples avanzadas, seguridad/pareo complejo ni el desarrollo de una app móvil dedicada._


---

## 4) Requisitos

**Software**
- _Arduino IDE 2.0+ con soporte ESP32 instalado._  
- _Librerías: BLEDevice.h, BLEUtils.h, BLEServer.h (incluidas en el core ESP32)._  

**Hardware**
- _ESP32 DevKit v1._  
- _Cable USB para programación._  
- _Dispositivo cliente con BLE (smartphone/tablet)._  

**Conocimientos previos**
- _Arduino/C++ básico._  
- _Conocimientos básicos de BLE y debugging por Serial._


---

## 5) Instalación / Código ⌨️

```bash
#include <BLEDevice.h>
#include <BLEUtils.h>
#include <BLEServer.h>

// ====== Configuración ======
static const char* DEVICE_NAME = "AKDevice";  // Nombre que verás en el escaneo
#define SERVICE_UUID        "12345678-1234-1234-1234-1234567890ab"
#define CHARACTERISTIC_UUID "abcd1234-5678-90ab-cdef-1234567890ab"

void setup() {
  Serial.begin(115200);
  delay(100);

  // 1) Inicializa BLE con nombre
  BLEDevice::init(DEVICE_NAME);

  // 2) Crea servidor y servicio
  BLEServer*      pServer  = BLEDevice::createServer();
  BLEService*     pService = pServer->createService(SERVICE_UUID);

  // 3) Característica de ejemplo (Lectura/Escritura)
  BLECharacteristic* pCharacteristic = pService->createCharacteristic(
      CHARACTERISTIC_UUID,
      BLECharacteristic::PROPERTY_READ   |
      BLECharacteristic::PROPERTY_WRITE
  );
  pCharacteristic->setValue("Hola BLE");

  // 4) Inicia el servicio
  pService->start();

  // 5) Configura el advertising
  BLEAdvertising* pAdvertising = BLEDevice::getAdvertising();
  pAdvertising->addServiceUUID(SERVICE_UUID);   // Anuncia el UUID del servicio
  pAdvertising->setScanResponse(true);          // Info extra en la respuesta de escaneo
  pAdvertising->setMinPreferred(0x06);          // Parámetros recomendados
  pAdvertising->setMinPreferred(0x12);

  // 6) Inicia advertising
  BLEDevice::startAdvertising();
  Serial.println("BLE Advertising started");
}

void loop() {
  // Nada en el loop; el anuncio corre en segundo plano
  delay(1000);
}
```
>En esta práctica se logró implementar la **comunicación Bluetooth Low Energy (BLE)** utilizando el ESP32 como servidor BLE.  
>Se comprendió la estructura del protocolo BLE, la diferencia entre **servicios y características**, y cómo establecer comunicación con un cliente BLE (por ejemplo, desde una app en un smartphone).  
>El resultado demuestra la capacidad del ESP32 para operar como un **dispositivo IoT eficiente y de bajo consumo**, capaz de transmitir información sin necesidad de WiFi, ideal para aplicaciones portátiles, sensores inteligentes y proyectos de automatización inalámbrica.  
