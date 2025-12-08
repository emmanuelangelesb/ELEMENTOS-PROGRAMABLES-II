# 📚 PROYECTO FINAL - ELEMENTOS PROGRAMABLES II
> El programa implementa una plataforma **Stewart** controlada por gestos de la mano mediante **visión por computadora**. Utiliza **MediaPipe** para detectar los landmarks de la mano (muñeca, dedo medio y pulgar) y calcula dos parámetros principales: el **pitch (inclinación arriba/abajo)** basado en la profundidad Z entre muñeca y dedo medio, y el **roll (inclinación izquierda/derecha)** determinado por la posición vertical del pulgar. Estos valores se procesan con un filtro exponencial para eliminar tembladera y se envían vía **Bluetooth Classic a un ESP32**, que controla 3 servomotores **MG90S** en configuración triangular (pines 4, 5 y 15). El ESP32 recibe comandos en formato **A1:x,A2:y,A3:z** y genera señales **PWM a 50Hz** con resolución de **12 bits** para posicionar cada servo. Refuerza conceptos de procesamiento de imagen en tiempo real, comunicación serial inalámbrica, control de actuadores y cinemática inversa básica para plataformas de movimiento.
---

## 1) Resumén 📌

- **Equipo / Autor(es):** Emmanuel Angeles Barreto
- **Curso / Asignatura:** Elementos Programables II  
- **Fecha:** 07/12/25  

---

## 2) Código ⌨️

```bash

```