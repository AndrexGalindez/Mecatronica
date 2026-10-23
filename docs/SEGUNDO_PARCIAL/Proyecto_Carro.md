# Bitácora de Proyecto: Carro RC de Fútbol

En esta sesión, Oliver nos enseñó a integrar la electrónica de potencia, el microcontrolador ESP32 y la comunicación inalámbrica vía Bluetooth para diseñar y programar un carro autónomo/controlado por remoto enfocado a competencias de fútbol robotizado.

## 👥 Equipo de Trabajo
Este proyecto se desarrolló en equipo de 4 integrantes, distribuyendo las responsabilidades de la siguiente manera:
- **Gael:** Programación del ESP32 y lógica de control.
- **Alan y Martín:** Diseño, ensamblaje y construcción física del chasis.
- **Yo (Andrés):** Apoyo en la consecución y preparación del material, colaborando también con Gael en la parte de programación.

## 🛠️ Materiales y Componentes Utilizados
- Tarjeta de desarrollo **ESP32**.
- Driver de motores **TB6612** (con pin de habilitación STBY).
- 2 Motores DC con caja reductora.
- Batería o fuente de alimentación externa.
- Módulo Bluetooth interno del ESP32 (`BluetoothSerial`).

---

## 💻 Código de Programación (C++)
A continuación se muestra el código implementado en el ESP32 para gestionar la recepción de comandos por Bluetooth, el control de velocidades mediante PWM adaptado a versiones de Arduino IDE y las funciones de movimiento omnidireccional/curvas para el control del carro de fútbol:

```cpp
#include "BluetoothSerial.h"
BluetoothSerial BT;

// Pines ESP32 -> TB6612
const int AIN1 = 26, AIN2 = 27, PWMA = 25;  // Motor izquierdo
const int BIN1 = 32, BIN2 = 33, PWMB = 14;  // Motor derecho
const int STBY = 13;

int velocidad = 170;  
char movimiento = 'S';      // último movimiento activo

void pwm(int pin, int valor) {
#if ESP_ARDUINO_VERSION_MAJOR >= 3
  ledcWrite(pin, valor);
#else
  ledcWrite(pin == PWMA ? 0 : 1, valor);
#endif
}

// Control de motores y curvas
void motores(int a1, int a2, int b1, int b2, int velA, int velB) {
  digitalWrite(AIN1, a1);
  digitalWrite(AIN2, a2);
  digitalWrite(BIN1, b1);
  digitalWrite(BIN2, b2);
  pwm(PWMA, velA);
  pwm(PWMB, velB);
}

// Direcciones principales
void adelante()  { motores(HIGH, LOW, HIGH, LOW, velocidad, velocidad); }
void atras()     { motores(LOW, HIGH, LOW, HIGH, velocidad, velocidad); }
void derecha()   { motores(HIGH, LOW, LOW, HIGH, velocidad, velocidad); }
void izquierda() { motores(LOW, HIGH, HIGH, LOW, velocidad, velocidad); }
void alto()      { motores(LOW, LOW, LOW, LOW, 0, 0); }

// Curvas tácticas para maniobrar en la cancha
void adelanteIzq() { motores(HIGH, LOW, HIGH, LOW, velocidad / 2, velocidad); }
void adelanteDer() { motores(HIGH, LOW, HIGH, LOW, velocidad, velocidad / 2); }
void atrasIzq()    { motores(LOW, HIGH, LOW, HIGH, velocidad / 2, velocidad); }
void atrasDer()    { motores(LOW, HIGH, LOW, HIGH, velocidad, velocidad / 2); }

void ejecutar(char m) {
  movimiento = m;
  switch (m) {
    case 'F': adelante();     break;
    case 'B': atras();        break;
    case 'L': izquierda();    break;
    case 'R': derecha();      break;
    case 'G': adelanteIzq();  break;   
    case 'H': adelanteDer();  break;   
    case 'I': atrasDer();     break;   
    case 'J': atrasIzq();     break;   
    default:  alto(); movimiento = 'S'; break;
  }
}

void setup() {
  Serial.begin(115200);                    // Monitor Serie (115200 baud)
  BT.begin("CarroRC_ATOM");

  int pines[] = {AIN1, AIN2, BIN1, BIN2, STBY};
  for (int p : pines) pinMode(p, OUTPUT);
  digitalWrite(STBY, HIGH);

#if ESP_ARDUINO_VERSION_MAJOR >= 3
  ledcAttach(PWMA, 1000, 8);
  ledcAttach(PWMB, 1000, 8);
#else
  ledcSetup(0, 1000, 8); ledcAttachPin(PWMA, 0);
  ledcSetup(1, 1000, 8); ledcAttachPin(PWMB, 1);
#endif
  alto();
}

void loop() {
  if (BT.available()) {
    char c = BT.read();

    if (c != '\r' && c != '\n') {          // ignora saltos de línea
      Serial.print("Recibido: ");          // para pruebas
      Serial.println(c);
    }

    if (c >= '0' && c <= '9') {            
      velocidad = map(c - '0', 0, 9, 90, 255);
      ejecutar(movimiento);                
    }
    else if (c == 'q') {                   
      velocidad = 255;
      ejecutar(movimiento);
    }
    else if (c != '\0' && strchr("FBLRGHIJS", c)) {    // movimientos y alto
      ejecutar(c);
    }
  }

  // Failsafe: si se pierde la conexión Bluetooth, frena el carro automáticamente
  if (!BT.hasClient()) alto();
}