#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <SoftwareSerial.h>
#define in1 2
#define in2 3
#define in3 4
#define in4 5
#define trig 8
#define echo 9
#define flame_analog A0  
#define flame_digital1 10
#define flame_digital2 11
#include "DHT.h"
#define DHTPIN A1
#define DHTTYPE DHT11
float humidity ;
float temperature ;
long duration, distance;
int flame_analog_reading = 0;
bool flame_digital1_reading = false;
bool flame_digital2_reading = false;
LiquidCrystal_I2C lcd(0x27, 16, 2);
DHT dht(DHTPIN, DHTTYPE);
void setup() {
  pinMode(in1, OUTPUT);
  pinMode(in2, OUTPUT);
  pinMode(in3, OUTPUT);
  pinMode(in4, OUTPUT);
  pinMode(trig, OUTPUT);
  pinMode(echo, INPUT);
  pinMode(flame_digital1, INPUT);
  pinMode(flame_digital2, INPUT);
  pinMode(flame_analog, INPUT);
  dht.begin();
  lcd.init();
  lcd.begin(16, 2);
  lcd.backlight();
  lcd.clear();
  Serial.begin(9600);
  lcd.print("Initialising..");
  delay(1000);
  lcd.clear();
}

void loop() {
  ultrasonic();
  flame_analog_reading = analogRead(flame_analog);
  flame_digital1_reading = digitalRead(flame_digital1);
  flame_digital2_reading = digitalRead(flame_digital2);
  float h = dht.readHumidity();
  float t = dht.readTemperature();
  if (distance < 20 && distance > 0) {
    right();
    delay(500);
  } else {
    forward();
  }
  if (Serial.available()) {
    if(flame_analog_reading < 800){
    Serial.println(" there is fire ");
  }
  else if (flame_digital1_reading == 0 || flame_digital2_reading == 0){
    Serial.println(" there is fire ");
  }
  else{
    Serial.println(" no fire       ");
  }
  }
  lcd.setCursor(0,0);
  lcd.print("T: ");
  lcd.setCursor(3,0);
  lcd.print(t);
  lcd.setCursor(0,1);
  lcd.print("H: ");
  lcd.setCursor(3,1);
  lcd.print(h);
}

void ultrasonic() {
  digitalWrite(trig, LOW);
  delayMicroseconds(2);
  digitalWrite(trig, HIGH);
  delayMicroseconds(10);
  digitalWrite(trig, LOW);
  duration = pulseIn(echo, HIGH);
  distance = duration * 0.0343 / 2;
}

void forward() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, HIGH);
  digitalWrite(in4, LOW);
}

void right() {
  digitalWrite(in1, HIGH);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, HIGH);
}

void Stop() {
  digitalWrite(in1, LOW);
  digitalWrite(in2, LOW);
  digitalWrite(in3, LOW);
  digitalWrite(in4, LOW);
}
