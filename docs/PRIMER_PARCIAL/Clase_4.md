# Bitácora 04: Tema 3 · Actuadores

En esta sesión, Oliver nos enseñó a controlar actuadores como motores de corriente directa y servomotores aplicando señales **PWM** (Modulación por Ancho de Pulso) desde el ESP32.

## 🛠️ Materiales y Herramientas
- ESP32.
- Driver de potencia (Puente H L298N o transistores MOSFET).
- Motor DC y Servomotor SG90.
- Fuente de alimentación externa.

## 📝 Desarrollo y Práctica
1. Configuración de canales PWM en el ESP32 para variar la velocidad de un motor DC a través del puente H.
2. Programación del control angular de un servomotor manipulando la anchura de pulso en microsegundos.
3. Separación de la alimentación lógica (ESP32) y la alimentación de potencia (motores) compartiendo una tierra común (GND).

## 💡 Observaciones y Conclusiones
Dividir las fuentes de alimentación evita que los picos de corriente que exigen los motores reinicien o quemen el microcontrolador.