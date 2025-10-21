# 📚 Práctica 3 - NeoPixel_ColorVariable

> El programa controla un LED NeoPixel mediante comunicación serial (Serial.begin(115200)), permitiendo cambiar dinámicamente el color RGB enviando un mensaje con formato específico "Red X, Green Y, Blue Z" (usando sscanf() para extraer valores y msj.trim() para limpiar espacios). Ocupamos conceptos de control de LEDs RGB con pixels.setPixelColor() y pixels.show(), procesamiento de cadenas de texto, variables dinámicas (int Red, Green, Blue) y condicionales. Es ideal para aprender la interacción avanzada entre Arduino y dispositivos externos mediante el puerto serial, permitiendo personalizar completamente el color del LED bajo demanda con Serial.printf() para retroalimentación.


---

## 1) Resumén 📌

- **Equipo / Autor(es):** Emmanuel Angeles Barreto
- **Curso / Asignatura:** Elementos Programables II  
- **Fecha:** 23/09/25  

---

## 2) Código ⌨️

```bash
# ========================================
# CÓDIGO 3: CONTROL DE LED NEOPIXEL CON COLOR RGB PERSONALIZABLE POR SERIAL
# ========================================

# Desde el primer include hasta el ultimo "define" llevan #
include <Adafruit_NeoPixel.h>  // Librería para controlar LEDs NeoPixel RGB direccionables
ifdef __AVR__
   include <avr/power.h>  // Librería específica para placas AVR (optimización de energía)
endif

# ========================================
# CONFIGURACIÓN DE CONSTANTES
# ========================================
define PIN  8          // Pin digital del ESP32 donde está conectado el LED NeoPixel
define NUMPIXELS 1     // Cantidad total de LEDs en la tira (en este caso solo 1)

# ========================================
# INICIALIZACIÓN DEL OBJETO NEOPIXEL
# ========================================
# Parámetros: cantidad de LEDs, pin de datos, tipo de LED (GRB) + frecuencia (800KHz)
Adafruit_NeoPixel pixels(NUMPIXELS, PIN, NEO_GRB + NEO_KHZ800);

# ========================================
# DECLARACIÓN DE VARIABLES GLOBALES PARA ALMACENAR COLORES RGB
# ========================================
int Red;    // Variable para almacenar el valor del color Rojo (0-255)
int Green;  // Variable para almacenar el valor del color Verde (0-255)
int Blue;   // Variable para almacenar el valor del color Azul (0-255)

# ========================================
# CONFIGURACIÓN INICIAL (se ejecuta una sola vez)
# ========================================
void setup(){
  Serial.begin(115200);  // Inicializar comunicación serial a 115200 baudios para recibir comandos RGB
  pixels.begin();        // Inicializar el sistema NeoPixel (configurar pin como salida)
}

# ========================================
# BUCLE PRINCIPAL (se ejecuta infinitamente)
# ========================================
void loop(){
  # Paso 1: Verificar si hay datos disponibles en el puerto serial
  if (Serial.available() > 0){
    
    # Paso 2: Leer el mensaje recibido hasta encontrar un salto de línea ('\n')
    String msj = Serial.readStringUntil('\n');
    
    # Paso 3: Limpiar espacios en blanco al inicio y final del mensaje
    # Esto asegura que el formato sea correcto para el procesamiento
    msj.trim();
    
    # Paso 4: Extraer los valores numéricos R, G, B de la cadena de texto
    # Formato esperado: "Red 255, Green 100, Blue 50"
    # sscanf() busca este patrón y guarda los números en las variables Red, Green, Blue
    # &Red, &Green, &Blue son las direcciones de memoria donde se guardan los valores
    sscanf(msj.c_str(), "Red %d, Green %d, Blue %d", &Red, &Green, &Blue);
    
    # Paso 5: Mostrar los valores extraídos en el Serial Monitor (retroalimentación)
    # Serial.printf() permite imprimir con formato tipo C
    Serial.printf("Valores: Red=%d, Green=%d, Blue=%d\n", Red, Green, Blue);
    
    # Paso 6: Apagar el LED antes de configurar el nuevo color
    pixels.clear();
    
    # Paso 7: Configurar el color del LED con los valores personalizados recibidos
    # Ahora el color es completamente variable según lo que envíe el usuario
    pixels.setPixelColor(0, pixels.Color(Red, Green, Blue));
    
    # Paso 8: Enviar los datos al LED para que muestre el color configurado
    pixels.show();
  }
  
  # Paso 9: Esperar 700 milisegundos (0.7 segundos) antes de la siguiente iteración
  delay(700);
  
  # IMPORTANTE: El LED se enciende con el color RGB que el usuario especifique
  # Ejemplo de mensaje a enviar: "Red 255, Green 0, Blue 0" para color rojo puro
  # El formato DEBE ser exacto para que sscanf() pueda extraer los valores correctamente
}

```
---

# 3) Evidencias del resultado final 📸

![Diagrama del sistema](recursos/imgs/ledcolor.jpg)

[VIDEO MUESTRA YT][doc-ref]

[doc-ref]: https://youtu.be/20FBP9kdGKE "Video muestra YT"


---
