# Tema 6 · Visión 101

En esta sesión, Oliver nos enseñó los conceptos básicos de procesamiento digital de imágenes y visión por computadora para la detección de objetos y patrones.

## 🛠️ Herramientas y Entorno
- Cámara web / Módulo de visión.
- Entorno con soporte para procesar imágenes (OpenCV / OpenMV / Python).

## 📝 Desarrollo y Práctica
1. Captura del flujo de video e interpretación de imágenes como matrices de píxeles.
2. Conversión del espacio de color RGB al espacio **HSV** para facilitar la segmentación por tono, saturación y brillo.
3. Aplicación de umbralización (*thresholding*) para aislar colores específicos e identificar contornos y centros de masa de los objetos.

## 💡 Observaciones y Conclusiones
La visión artificial es altamente sensible a las condiciones de luz del entorno, por lo que calibrar adecuadamente los rangos HSV en cada espacio de trabajo es imprescindible.