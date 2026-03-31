# Robotics-2#include <Servo.h>

// Pin definitions 
int trigPin = 9;
int echoPin = 10;

int redLED = 2; 
int yellowLED = 3;
int greenLED = 4;

int servoPin = 6;

Servo gateServo;

long duration;
int distance;

// Threshold distance (cm)
int threshold = 15;

// ---------------- SETUP ----------------
void setup() {
  Serial.begin(9600);

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);

  pinMode(redLED, OUTPUT);
  pinMode(yellowLED, OUTPUT);
  pinMode(greenLED, OUTPUT);

  gateServo.attach(servoPin);
  gateServo.write(0); // Gate closed

  Serial.println("System Ready...");
}

// ---------------- LOOP ----------------
void loop() {
  distance = getDistance();
  Serial.print("Distance: ");
  Serial.print(distance);
  Serial.println(" cm");

  controlSystem(distance);

  delay(500);
}

// ---------------- FUNCTION: GET DISTANCE ----------------
int getDistance() {
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);

  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);

  int dist = duration * 0.034 / 2;

  return dist;
}

// ---------------- FUNCTION: CONTROL SYSTEM ----------------
void controlSystem(int dist) {

  if (dist > 0 && dist < threshold) {
    // Object close → OPEN gate
    gateServo.write(90);

    digitalWrite(greenLED, HIGH);
    digitalWrite(yellowLED, LOW);
    digitalWrite(redLED, LOW);

    Serial.println("Gate OPEN - GREEN light");

  } else if (dist >= threshold && dist < threshold + 10) {
    // Warning zone
    gateServo.write(0);

    digitalWrite(greenLED, LOW);
    digitalWrite(yellowLED, HIGH);
    digitalWrite(redLED, LOW);

    Serial.println("WAIT - YELLOW light");

  } else {
    // No object → CLOSED gate
    gateServo.write(0);

    digitalWrite(greenLED, LOW);
    digitalWrite(yellowLED, LOW);
    digitalWrite(redLED, HIGH);

    Serial.println("STOP - RED light");
  }
}
