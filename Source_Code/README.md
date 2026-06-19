#include <DHT.h>
#include <ESP32Servo.h>

#define DHTPIN 15
#define DHTTYPE DHT22

#define SOIL_PIN 34
#define TRIG 5
#define ECHO 4

Servo sprayMotor;
DHT dht(DHTPIN, DHTTYPE);

void setup() {

  Serial.begin(115200);

  dht.begin();   // start DHT sensor

  pinMode(TRIG, OUTPUT);
  pinMode(ECHO, INPUT);

  sprayMotor.attach(18);   // servo pin

  sprayMotor.write(0);     // spray OFF
}

void loop() {

  // sensor readings
  float temperature = dht.readTemperature();
  float humidity = dht.readHumidity();
  int soilValue = analogRead(SOIL_PIN);

  // ultrasonic sensor
  digitalWrite(TRIG, LOW);
  delayMicroseconds(2);

  digitalWrite(TRIG, HIGH);
  delayMicroseconds(10);

  digitalWrite(TRIG, LOW);

  long duration = pulseIn(ECHO, HIGH);
  float cropDistance = duration * 0.034 / 2;

  // display data
  Serial.print("Temperature = ");
  Serial.println(temperature);

  Serial.print("Humidity = ");
  Serial.println(humidity);

  Serial.print("Soil = ");
  Serial.println(soilValue);

  Serial.print("Distance = ");
  Serial.println(cropDistance);

  // spray condition
  if (soilValue < 1500 && cropDistance < 50) {

    sprayMotor.write(90);   // start spray

    Serial.println("Spraying ON");
  }
  else {

    sprayMotor.write(0);    // stop spray

    Serial.println("Spraying OFF");
  }

  delay(2000);
}
