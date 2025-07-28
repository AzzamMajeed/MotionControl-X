# MotionControl-X
# 🤖 Arduino Robot Project – Motor Control and Obstacle Avoidance

This project includes two main tasks using Arduino and Tinkercad:

---

## ✅ Task 1: Motor Control Sequence

The robot performs:<img width="1280" height="488" alt="Sizzling Crift-Jaban" src="https://github.com/user-attachments/assets/3960b707-f056-49fd-a6f6-a4b24da978cc" />

- Forward movement for 30 seconds  
- Backward movement for 1 minute  
- Alternating between right and left

### Components:
- 4 × DC Motors  
- L293D Motor Driver  
- Arduino Uno  
- Breadboard + jumper wires

---

## ✅ Task 2: Obstacle Detection and Reaction
 <img width="1280" height="488" alt="Brilliant Migelo" src="https://github.com/user-attachments/assets/4274e3da-b3c5-4fa2-83f4-4ad7541f1c91" />
The robot detects obstacles using an ultrasonic sensor and reacts by:
- Stopping all movement  
- Rotating a servo motor 

- Reversing for 2 seconds  
- Resuming normal operation when the path is clear

### Additional Components:
- 1 × Ultrasonic Sensor (3-pin version)  
- 1 × Servo Motor

---

## 🧩 Wiring Summary

### L293D to Arduino:

| L293D Pin | Arduino Pin | Function             |
|-----------|-------------|----------------------|
| 1 (EN1)   | 10          | Right motor enable   |
| 2 (IN1)   | 9           | Right motor direction |
| 7 (IN2)   | 8           | Right motor direction |
| 9 (EN2)   | 5           | Left motor enable    |
|10 (IN3)   | 6           | Left motor direction |
|15 (IN4)   | 7           | Left motor direction |
|16 (Vcc1)  | 5V          | Logic power          |
| 8 (Vcc2)  | 5V or 9V    | Motor power          |
| 4,5,12,13 | GND         | Ground               |

### Ultrasonic Sensor:

| Pin   | Arduino |
|-------|---------|
| VCC   | 5V      |
| GND   | GND     |
| TRIG  | 12      |
| ECHO  | 11      |

### Servo Motor:

| Wire   | Arduino |
|--------|---------|
| VCC    | 5V      |
| GND    | GND     |
| Signal | 3       |

---

## 🧠 Features Implemented

- L293D driver with dual motor control  
- Obstacle detection via ultrasonic sensor  
- Servo rotation upon obstacle detection  
- Directional control using digital outputs  
- Movement sequencing with `delay()` and condition checks

---

## 💻 Arduino Code (Combined)

```cpp
#include <Servo.h>

// Motor pins
#define IN1 9
#define IN2 8
#define IN3 6
#define IN4 7
#define ENA 10
#define ENB 5

// Ultrasonic pins
#define trigPin 12
#define echoPin 11

Servo myServo;

void setup() {
  // Set motor pins
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);

  // Set ultrasonic pins
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // Attach servo
  myServo.attach(3);
  myServo.write(90); // Neutral position

  // TASK 1: Movement Sequence
  moveForward();
  delay(30000); // 30 seconds

  moveBackward();
  delay(60000); // 1 minute

  // TASK 2: Loop with obstacle detection
}

void loop() {
  // Alternate left and right
  turnRight();
  delay(1000);
  turnLeft();
  delay(1000);

  long distance = readDistanceCM();
  if (distance > 0 && distance < 10) {
    stopMotors();
    myServo.write(0);      // Turn servo
    delay(1000);
    moveBackward();        // Move backward
    delay(2000);
    stopMotors();
    myServo.write(90);     // Reset servo
  } else {
    moveForward();         // Resume forward
    myServo.write(90);
  }
}

// Movement functions
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

// Ultrasonic function
long readDistanceCM() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH);
  long distance = duration * 0.034 / 2;
  return distance;
}
