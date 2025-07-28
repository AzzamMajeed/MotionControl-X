# 🤖 Arduino Smart Robot – Movement & Obstacle Detection

## 📌 Project Overview

This Arduino project combines **two tasks**:

- **Task 1**: Control 4 DC motors to move:
  - Forward for 30 seconds  
  - Backward for 1 minute  
  - Alternate between right and left turns continuously<img width="1280" height="488" alt="Sizzling Crift-Jaban" src="https://github.com/user-attachments/assets/e1906bc1-785b-4c26-870e-e8df3dee0d7f" />


- **Task 2**: Use a **Servo motor** with an **Ultrasonic sensor** to:
  - Detect objects within 10 cm  
  - Stop all motors  
  - Rotate the servo  
  - Move backward briefly  
  - Resume motion

---
<img width="1280" height="488" alt="Brilliant Migelo" src="https://github.com/user-attachments/assets/fc4c6a8c-dd7b-4e30-9ea8-5f7f6dc49ed1" />

## 🧰 Components Used

| Component              | Quantity |
|------------------------|----------|
| Arduino UNO            | 1        |
| L293D Motor Driver IC  | 1        |
| DC Motors              | 4        |
| Ultrasonic Sensor (HC-SR04) | 1    |
| Servo Motor            | 1        |
| Breadboard + Wires     | As needed |
| Power Supply (e.g., 9V) | 1       |

---

## ⚡ Circuit Wiring

### 🔌 DC Motor Connections (via L293D):

| L293D Pin | Connection         |
|-----------|--------------------|
| 1         | Arduino 10 (ENA)   |
| 2         | Arduino 9 (IN1)    |
| 3         | Motor A (+)        |
| 4, 5      | GND                |
| 6         | Motor A (–)        |
| 7         | Arduino 8 (IN2)    |
| 8         | VCC (e.g., 9V)     |
| 9         | Arduino 5 (ENB)    |
| 10        | Arduino 6 (IN3)    |
| 11        | Motor B (+)        |
| 12, 13    | GND                |
| 14        | Motor B (–)        |
| 15        | Arduino 7 (IN4)    |
| 16        | 5V (Arduino)       |

### 📏 Ultrasonic Sensor:

| Sensor Pin | Arduino Pin |
|------------|-------------|
| VCC        | 5V          |
| GND        | GND         |
| Trig       | 12          |
| Echo       | 11          |

### 🔄 Servo Motor:

| Servo Wire | Arduino Pin |
|------------|-------------|
| Signal     | 3           |
| VCC        | 5V          |
| GND        | GND         |

---

## 🧠 Code

```cpp
#include <Servo.h>

// Motor pins
#define IN1 9
#define IN2 8
#define IN3 6
#define IN4 7
#define ENA 10
#define ENB 5

// Ultrasonic sensor pins
#define trigPin 12
#define echoPin 11

// Servo
Servo myServo;

void setup() {
  // Motor setup
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);

  // Sensor and Servo setup
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  myServo.attach(3);
  myServo.write(90);  // Neutral position

  // --- Task 1: Movement Sequence ---
  moveForward();
  delay(30000);  // Forward 30 seconds

  moveBackward();
  delay(60000);  // Backward 1 minute
}

void loop() {
  // Alternate turning
  turnRight();
  delay(1000);
  turnLeft();
  delay(1000);

  // --- Task 2: Obstacle Detection ---
  long distance = readDistanceCM();
  if (distance > 0 && distance < 10) {
    stopMotors();
    myServo.write(0);    // Servo moves to one side
    delay(1000);
    moveBackward();
    delay(2000);
    stopMotors();
    myServo.write(90);   // Back to center
  } else {
    moveForward();  // Keep moving forward
  }
}

// -------------------------
// Movement Functions
// -------------------------

void moveForward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

void moveBackward() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

void turnRight() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
}

void turnLeft() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
}

void stopMotors() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
}

// -------------------------
// Ultrasonic Sensor Reading
// -------------------------

long readDistanceCM() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH);
  return duration * 0.034 / 2;
}
