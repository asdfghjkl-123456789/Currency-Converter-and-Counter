#include <Wire.h>
#include <LiquidCrystal_I2C.h>

// Set the LCD address to 0x27 for a 16x2 display
LiquidCrystal_I2C lcd(0x27, 16, 2);

// Pin definitions
const int irSensorPin = A0;
const int buttonMenu = 2;
const int buttonUp = 3;
const int buttonDown = 4;
const int buttonSelect = 5;

// Currency variables
float totalAmount = 0.0;
int billCount = 0;
int currentCurrency = 0; // 0=USD, 1=EUR, 2=GBP, 3=JPY

// Exchange rates (example values - should be updated regularly)
const float exchangeRates[] = {
  1.0,    // USD to USD
  0.85,   // USD to EUR
  0.73,   // USD to GBP
  110.25  // USD to JPY
};
const char* currencySymbols[] = {"$", "€", "£", "¥"};

// Bill detection variables
bool billDetected = false;
unsigned long lastDetectionTime = 0;
const unsigned long debounceDelay = 200; // ms

// Menu system
int menuState = 0; // 0=main, 1=currency select, 2=reset confirm

void setup() {
  // Initialize serial communication
  Serial.begin(9600);
  
  // Initialize LCD
  lcd.init();
  lcd.backlight();
  
  // Set pin modes
  pinMode(irSensorPin, INPUT);
  pinMode(buttonMenu, INPUT);
  pinMode(buttonUp, INPUT);
  pinMode(buttonDown, INPUT);
  pinMode(buttonSelect, INPUT);
  
  // Display startup message
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Currency Counter");
  lcd.setCursor(0, 1);
  lcd.print("Initializing...");
  delay(2000);
  
  displayMainScreen();
}

void loop() {
  // Check for bill detection
  checkBill();
  
  // Check button presses
  checkButtons();
  
  // Small delay to prevent flickering
  delay(50);
}

void checkBill() {
  int sensorValue = analogRead(irSensorPin);
  
  // Detect when a bill passes through (IR beam broken)
  if (sensorValue < 500 && !billDetected && millis() - lastDetectionTime > debounceDelay) {
    billDetected = true;
    lastDetectionTime = millis();
    
    // Increment count and amount (assuming $1 bills for this example)
    billCount++;
    totalAmount += 1.0;
    
    Serial.print("Bill detected. Total: ");
    Serial.print(totalAmount);
    Serial.println(currencySymbols[currentCurrency]);
    
    if (menuState == 0) {
      displayMainScreen();
    }
  }
  else if (sensorValue > 500) {
    billDetected = false;
  }
}

void checkButtons() {
  static unsigned long lastButtonPress = 0;
  
  // Debounce check
  if (millis() - lastButtonPress < 200) {
    return;
  }
  
  if (digitalRead(buttonMenu) {
    lastButtonPress = millis();
    menuState = (menuState + 1) % 3;
    
    if (menuState == 0) {
      displayMainScreen();
    } else if (menuState == 1) {
      displayCurrencyMenu();
    } else {
      displayResetMenu();
    }
  }
  
  if (menuState == 1 && digitalRead(buttonUp)) {
    lastButtonPress = millis();
    currentCurrency = (currentCurrency + 1) % 4;
    displayCurrencyMenu();
  }
  
  if (menuState == 1 && digitalRead(buttonDown)) {
    lastButtonPress = millis();
    currentCurrency = (currentCurrency - 1 + 4) % 4;
    displayCurrencyMenu();
  }
  
  if (menuState == 2 && digitalRead(buttonSelect)) {
    lastButtonPress = millis();
    // Reset counters
    totalAmount = 0;
    billCount = 0;
    menuState = 0;
    displayMainScreen();
    Serial.println("Counters reset");
  }
}

void displayMainScreen() {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Bills: ");
  lcd.print(billCount);
  
  lcd.setCursor(0, 1);
  lcd.print("Total: ");
  lcd.print(convertCurrency(totalAmount));
  lcd.print(currencySymbols[currentCurrency]);
}

void displayCurrencyMenu() {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Select Currency:");
  
  lcd.setCursor(0, 1);
  switch(currentCurrency) {
    case 0: lcd.print("USD"); break;
    case 1: lcd.print("EUR"); break;
    case 2: lcd.print("GBP"); break;
    case 3: lcd.print("JPY"); break;
  }
  lcd.print(" ");
  lcd.print(convertCurrency(1.0)); // Show conversion of 1 USD
  lcd.print(currencySymbols[currentCurrency]);
}

void displayResetMenu() {
  lcd.clear();
  lcd.setCursor(0, 0);
  lcd.print("Reset Counters?");
  
  lcd.setCursor(0, 1);
  lcd.print("Press SELECT");
}

float convertCurrency(float amountUSD) {
  return amountUSD * exchangeRates[currentCurrency];
}
