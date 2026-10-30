#include <LiquidCrystal.h>

// LCD pins: RS, E, D4, D5, D6, D7
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

// Motor driver pins
const int ENA = 10;
const int IN1 = 8;
const int IN2 = 9;

// Push button
const int button = 6;

bool motorState = false;

int buttonState;
int lastButtonState = HIGH;

unsigned long lastDebounceTime = 0;
unsigned long lastDisplayTime = 0;

const unsigned long debounceDelay = 50;

byte smiley[8] = {
  B00000,
  B01010,
  B01010,
  B00000,
  B10001,
  B01110,
  B00000,
  B00000
};

void setup() {

  lcd.begin(16, 2);

  pinMode(ENA, OUTPUT);
  pinMode(IN1, OUTPUT);
  pinMode(IN2, OUTPUT);

  pinMode(button, INPUT_PULLUP);

  lcd.createChar(0, smiley);

  lcd.setCursor(0, 0);
  lcd.print("LCD MOTOR SYSTEM");

  lcd.setCursor(0, 1);
  lcd.print("Initializing...");

  delay(2000);

  lcd.clear();
}

void loop() {

  int reading = digitalRead(button);

  if (reading != lastButtonState) {
    lastDebounceTime = millis();
  }

  if ((millis() - lastDebounceTime) > debounceDelay) {

    if (reading != buttonState) {

      buttonState = reading;

      if (buttonState == LOW) {

        motorState = !motorState;
      }
    }
  }

  lastButtonState = reading;

  // Motor control
  if (motorState) {

    digitalWrite(IN1, HIGH);
    digitalWrite(IN2, LOW);
    analogWrite(ENA, 200);

  } else {

    digitalWrite(IN1, LOW);
    digitalWrite(IN2, LOW);
    analogWrite(ENA, 0);
  }

  // LCD display update
  if (millis() - lastDisplayTime >= 500) {

    lastDisplayTime = millis();

    lcd.setCursor(0, 0);
    lcd.print("SYSTEM STATUS   ");

    lcd.setCursor(0, 1);

    if (motorState) {
      lcd.print("MOTOR: ON ");
    } else {
      lcd.print("MOTOR: OFF");
    }

    lcd.write(byte(0));
  }
}# LCD_Embedded_System_Project
An embedded systems project focused on interfacing an LCD display with a microcontroller to display text and information. This project demonstrates LCD interfacing, hardware connections, and embedded C programming.
