#include <Wire.h>
#include <LiquidCrystal_I2C.h>
#include <Adafruit_INA219.h>

LiquidCrystal_I2C lcd(0x27, 16, 2);
Adafruit_INA219 ina219;

#define POT A0

int relayPins[4] = {2, 3, 4, 5};

int previousStage = -1;

void setup() {

  Serial.begin(9600);
  Wire.begin();

  lcd.init();
  lcd.backlight();

  // Initialize relay pins
  for (int i = 0; i < 4; i++) {
    pinMode(relayPins[i], OUTPUT);

  }

  // INA219 initialization
  if (!ina219.begin()) {
    lcd.print("INA219 Error!");
    while (1);
  }

  lcd.print("Load Management");
  delay(1500);
  lcd.clear();
}

void loop() {

  int potValue = analogRead(POT);

  // Four stages
  int stage = (potValue / 256) + 1;

  if (stage > 4) {
    stage = 4;
  }

  // Cumulative switching with NC connections
  if (stage != previousStage) {


  }

  // INA219 readings
  float voltage = ina219.getBusVoltage_V();
  float current = ina219.getCurrent_mA();
  float power = ina219.getPower_mW();

  lcd.setCursor(0, 0);
  lcd.print("POT:");
  lcd.print(potValue);
  lcd.print(" L:");
  lcd.print(stage);
  lcd.print("  ");

  lcd.setCursor(0, 1);
  lcd.print("I:");
  lcd.print(current, 0);
  lcd.print("mA     ");

  Serial.print("POT: ");
  Serial.print(potValue);

  Serial.print(" | Stage: ");
  Serial.print(stage);

  Serial.print(" | Voltage: ");
  Serial.print(voltage);

  Serial.print("V | Current: ");
  Serial.print(current);

  Serial.print("mA | Power: ");
  Serial.print(power);
  Serial.println("mW");

  delay(300);
}
