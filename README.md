# IOTproject0.98
Code to be written in the Arduino to calculate the weight of the cyclinder and to minus some amount of gas to alert the user for minimum amount of gas :

#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <SoftwareSerial.h>
#include <HX711.h>

#define MQ2pin A3
#define LOADCELL_DOUT_PIN A1
#define LOADCELL_SCK_PIN A0

LiquidCrystal_I2C lcd(0x3F, 16, 2);
SoftwareSerial gsmSerial(10, 11);
HX711 scale;

float sensorValue;
float weight;

void setup() {
  Serial.begin(9600);
  lcd.init();
  lcd.clear();
  lcd.backlight();
  lcd.setCursor(0,0);
  lcd.print("Gas Sensor");
  lcd.setCursor(0,1);
  lcd.print("Warming Up...");

  delay(2000);

  scale.begin(LOADCELL_DOUT_PIN, LOADCELL_SCK_PIN);
  scale.set_scale(2280.0); // adjust this calibration value to match your load cell
  scale.tare();

  gsmSerial.begin(9600);
}

void loop() {
  sensorValue = analogRead(MQ2pin);

  lcd.setCursor(0,0);
  lcd.print("Gas Sensor:");
  lcd.print(sensorValue);
  // lcd.clear();

  weight = scale.get_units();

  lcd.setCursor(0,1);
  lcd.print("Weight:");
  lcd.print(weight);
  lcd.print(" g");
  delay(800);

  Serial.print("Gas Sensor: ");
  Serial.print(sensorValue);
  Serial.print(" | Weight: ");
  Serial.print(weight);
  Serial.println(" g");

  if (weight < 1200) {
    // lcd.clear();
    String message = "Weight is less than 2kg: " + String(weight) + "g";
    gsmSerial.println("ATD+919751585762;"); // replace with your phone number
    delay(2000);
    gsmSerial.println("ATH");
    delay(500);
    Serial.println("Missed call made");
    lcd.clear();
    lcd.print("weight Alert");
    lcd.setCursor(0,1);
    lcd.print("weight call made");
  }
  if(sensorValue>600)
  {
    lcd.clear();
    lcd.print("Aleart !!");
    lcd.setCursor(0,1);
    lcd.print("Gas call made");


  }

  delay(2000);
}

Code to detect the gas leakage :
#define MQ2pin (0)

float sensorValue;  //variable to store sensor value

void setup()
{
  Serial.begin(9600); // sets the serial port to 9600
  Serial.println("Gas sensor warming up!");
  delay(20000); // allow the MQ-2 to warm up
}

void loop()
{
  sensorValue = analogRead(MQ2pin); // read analog input pin 0
  
  Serial.print("Sensor Value: ");
  Serial.print(sensorValue);
  
  if(sensorValue > 600)
  {
    Serial.print(" | Smoke detected!");
  }
  
  Serial.println("");
  delay(2000); // wait 2s for next reading
}
