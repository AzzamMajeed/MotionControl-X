# 🤖 Arduino Dual Projects – Motor Control & Obstacle Detection

This repository includes two separate Arduino projects using DC motors, servo, and ultrasonic sensor on Tinkercad.

---

## 🧪 Project 1: Timed Motion Robot (Motor Control)

### 📌 Objective:
Control 4 DC motors using an L293D IC to:
- Move **forward for 30 seconds**
- Move **backward for 1 minute**
- Then **alternate turning right and left repeatedly**

### 🧩 Components:
- Arduino UNO
- 4 × DC Motors
- L293D Motor Driver
- Power source (or simulated in Tinkercad)

### 🔌 Connections:
| L293D Pin | Arduino Pin |
|----------|--------------|
| IN1      | 9            |
| IN2      | 8            |
| IN3      | 6            |
| IN4      | 7            |
| ENA      | 10 (PWM)     |
| ENB      | 5  (PWM)     |

Motors connect to:
- M1: IN1 & IN2
- M2: IN3 & IN4

### 🖼️ Project Image:
> <img width="1280" height="488" alt="Sizzling Crift-Jaban" src="https://github.com/user-attachments/assets/a814dee6-abce-489f-9b50-68fa5de70257" />

### 🧠 Code:

```cpp
#define IN1 9
#define IN2 8
#define IN3 6
#define IN4 7
#define ENA 10
#define ENB 5

void setup() {
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);

  // Move forward for 30 seconds
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
  delay(30000);

  // Move backward for 60 seconds
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  delay(60000);
}

void loop() {
  // Turn right
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  delay(1000);

  // Turn left
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  delay(1000);
}


# 🛠️ Project 2: Obstacle-Avoiding Robot (Arduino)

## 📌 Overview

This project demonstrates an autonomous robot using:
- **Ultrasonic Sensor** to detect obstacles
- **Servo Motor** to react when an object is near
- **Two DC motors (via L293D driver)** to move forward or backward

When the robot detects an object closer than 10 cm, it:
- Stops
- Moves the servo to 0°
- Reverses for a short time
- Then resumes moving forward

---

## 🔌 Components and Connections

| Component         | Arduino Pin | Notes              |
|------------------|-------------|--------------------|
| Ultrasonic - Trig| 12          | Output signal      |
| Ultrasonic - Echo| 11          | Input signal       |
| Servo Motor      | 3           | PWM pin            |
| Motor A - IN1    | 9           | L293D input        |
| Motor A - IN2    | 8           | L293D input        |
| Motor B - IN3    | 6           | L293D input        |
| Motor B - IN4    | 7           | L293D input        |
| Motor A - ENA    | 10          | PWM speed control  |
| Motor B - ENB    | 5           | PWM speed control  |

**Power**:
- Connect motors to external 5V if needed (for stronger power)
- Don’t power motors directly from Arduino 5V pin

---

## 🖼️ Project Image<img

<img width="1280" height="488" alt="Brilliant Migelo" src="https://github.com/user-attachments/assets/9cb9197b-8333-41dc-887e-06bc57582bff" />


## 🧠 Code Explanation

- `getDistance()` reads from the ultrasonic sensor.
- If the distance is less than 10 cm:
  - The robot stops and the servo moves to 0°
  - Reverses for 1 second
  - Returns servo to neutral (90°)
- If distance ≥ 10 cm:
  - Moves forward

---

## 💻 Code

```cpp
#include <Servo.h>

// Define motor pins
#define IN1 9
#define IN2 8
#define IN3 6
#define IN4 7
#define ENA 10
#define ENB 5

// Define ultrasonic sensor pins
#define trigPin 12
#define echoPin 11

Servo myServo;

void setup() {
  // Motor pins
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);
  pinMode(IN3, OUTPUT);
  pinMode(IN4, OUTPUT);
  pinMode(ENA, OUTPUT);
  pinMode(ENB, OUTPUT);

  // Ultrasonic pins
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  // Servo setup
  myServo.attach(3);
  myServo.write(90);  // Neutral position
}

void loop() {
  long distance = getDistance();

  if (distance > 0 && distance < 10) {
    // Obstacle detected
    stopMotors();
    myServo.write(0);     // Move servo to look left
    delay(1000);
    moveBackward();
    delay(1000);
    stopMotors();
    myServo.write(90);    // Reset servo
  } else {
    moveForward();
  }
}

// Get distance from ultrasonic sensor
long getDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  long duration = pulseIn(echoPin, HIGH);
  return duration * 0.034 / 2;  // Convert to cm
}

// Move forward
void moveForward() {
  digitalWrite(IN1, HIGH);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, HIGH);
  digitalWrite(IN4, LOW);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

// Move backward
void moveBackward() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, HIGH);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, HIGH);
  analogWrite(ENA, 200);
  analogWrite(ENB, 200);
}

// Stop motors
void stopMotors() {
  digitalWrite(IN1, LOW);
  digitalWrite(IN2, LOW);
  digitalWrite(IN3, LOW);
  digitalWrite(IN4, LOW);
}

