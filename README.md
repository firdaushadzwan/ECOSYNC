# ECOSYNC
Smart energy-efficient air conditioning controller
# EcoSync – Smart Energy-Efficient Air Conditioning Controller

EcoSync is a prototype smart air conditioning control system designed to improve energy efficiency by dynamically adjusting compressor speed based on real-time temperature readings.

The system uses an **Arduino microcontroller**, **DHT11 temperature sensor**, and a **stepper motor (simulated compressor)** to demonstrate how modern air conditioners can regulate cooling output more efficiently compared to traditional ON/OFF systems.

This project was developed as part of the **KIG2020 Mechatronics II course** at the Department of Mechanical Engineering, University of Malaya.

---

## Project Objectives

- Develop an automatic temperature control system using Arduino.
- Maintain optimal indoor temperature using real-time sensor feedback.
- Simulate a **variable-speed air conditioning compressor** using a stepper motor.
- Improve energy efficiency by reducing unnecessary ON/OFF cycling.

---

## System Overview

The system continuously reads ambient temperature using the **DHT11 sensor** and adjusts the **stepper motor speed** accordingly:

| Temperature Range | Motor Speed | Behaviour |
|------------------|-------------|-----------|
| Below 26°C | Low Speed (3 RPM) | Cooling demand low |
| 26°C – 32°C | Medium Speed (8 RPM) | Normal cooling |
| Above 32°C | High Speed (11 RPM) | Maximum cooling |

This simulates the behavior of **variable-speed compressors used in modern energy-efficient air conditioning systems.**

---

## Hardware Components

- Arduino Uno R3
- DHT11 Temperature Sensor
- 28BYJ-48 Stepper Motor
- ULN2003 Stepper Motor Driver
- Breadboard
- Jumper Wires

---

## Circuit Connection

### DHT11 Sensor
- VCC → Arduino Vin
- DATA → Arduino Pin 7
- GND → Arduino GND

### ULN2003 Stepper Motor Driver
- VCC → Arduino 5V
- GND → Arduino GND
- IN1 → Arduino Pin 8
- IN2 → Arduino Pin 10
- IN3 → Arduino Pin 9
- IN4 → Arduino Pin 11

---

## Arduino Code

```cpp
#include <Adafruit_Sensor.h>
#include <DHT.h>
#include <DHT_U.h>
#include <Stepper.h>

#define DHTPIN 7
#define DHTTYPE DHT11

const int stepsPerRevolution = 2048;

Stepper stepper(stepsPerRevolution, 8, 10, 9, 11);

#define TEMPERATURE_THRESHOLD 26
#define TEMPERATURE_THRESHOLD1 32

DHT dht(DHTPIN, DHTTYPE);

float currentRPM = 0;

void setup() {
  Serial.begin(9600);
  dht.begin();

  stepper.setSpeed(5);
  currentRPM = 5;
}

void loop() {
  float temperature = dht.readTemperature();

  if (isnan(temperature)) {
    Serial.println("Failed to read temperature from DHT sensor!");
    return;
  }

  if (temperature > TEMPERATURE_THRESHOLD1) {
    currentRPM = 11;
    stepper.setSpeed(currentRPM);
    stepper.step(stepsPerRevolution);
  }
  else if (temperature > TEMPERATURE_THRESHOLD) {
    currentRPM = 8;
    stepper.setSpeed(currentRPM);
    stepper.step(stepsPerRevolution / 2);
  }
  else {
    currentRPM = 3;
    stepper.setSpeed(currentRPM);
    stepper.step(stepsPerRevolution / 4);
  }

  Serial.print("Temperature: ");
  Serial.print(temperature);
  Serial.println(" °C");

  Serial.print("Motor Speed: ");
  Serial.print(currentRPM);
  Serial.println(" RPM");
  Serial.println("--------------------");
}
