#include <Servo.h> 

// ---------------- PIN DEFINITIONS ----------------
const int trigPin = 9;
const int echoPin = 10;

const int redLED = 2;
const int yellowLED = 3;
const int greenLED = 4;

const int servoPin = 6;

// ---------------- VARIABLES ----------------
Servo gateServo;

long duration;
int distance;

const int threshold = 15; // cm

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

  duration = pulseIn(echoPin, HIGH, 30000); // timeout

  if (duration == 0) {
    return 999; // No object detected
  }

  return duration * 0.034 / 2;
}

// ---------------- FUNCTION: CONTROL SYSTEM ----------------
void controlSystem(int dist) {

  if (dist > 0 && dist < threshold) {
    // OPEN gate
    gateServo.write(90);

    digitalWrite(greenLED, HIGH);
    digitalWrite(yellowLED, LOW);
    digitalWrite(redLED, LOW);

    Serial.println("OPEN - GREEN");

  } else if (dist >= threshold && dist < threshold + 10) {
    // WARNING
    gateServo.write(0);

    digitalWrite(greenLED, LOW);
    digitalWrite(yellowLED, HIGH);
    digitalWrite(redLED, LOW);

    Serial.println("WAIT - YELLOW");

  } else {
    // CLOSED
    gateServo.write(0);

    digitalWrite(greenLED, LOW);
    digitalWrite(yellowLED, LOW);
    digitalWrite(redLED, HIGH);

    Serial.println("STOP - RED");
  }
}
