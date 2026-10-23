# Bitácora 05: Tema 4 · Sensores

En esta sesión, Oliver nos enseñó a conectar y calibrar sensores analógicos y digitales con el ESP32 para tomar lecturas de variables del entorno físico.

## 🛠️ Materiales y Herramientas
- ESP32.
- Sensor ultrasónico HC-SR04 / Fotorresistencia (LDR) / Sensor analógico.
- Protoboard y jumpers.

## 📝 Desarrollo y Práctica
1. Uso del convertidor analógico a digital (**ADC**) del ESP32 para interpretar variaciones de voltaje de los sensores.
2. Programación de un pulso de disparo (*Trigger*) y medición del tiempo de eco (*Echo*) en el sensor ultrasónico para calcular distancias en centímetros.
3. Mapeo e interpretación de los datos para visualizarlos de manera gráfica y numérica en la consola serial.

## 💡 Observaciones y Conclusiones
Las lecturas de los sensores analógicos suelen presentar ruido eléctrico; aplicar pequeños promedios o filtros por software ayuda a estabilizar la medición.