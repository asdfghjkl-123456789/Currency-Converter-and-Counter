Project Description
This Arduino-based project combines hardware and software to create a functional currency counter and converter system. The device counts bills as they pass through an infrared sensor and provides real-time conversion between multiple currencies.

Features
Bill Counting: Detects and counts bills using an infrared sensor system
Multi-Currency Support: Converts between USD, EUR, GBP, and JPY (easily expandable)

User Interface:
16x2 LCD display for clear output
Button-based menu navigation
Real-time Conversion: Displays totals in selected currency with proper symbols
Reset Functionality: Option to clear counters when needed

Hardware Components
Arduino Uno or compatible board
Infrared sensor pair (emitter and receiver)
16x2 LCD display with I2C interface
Tactile push buttons (4x)
Breadboard and jumper wires
Resistors (220Ω and 10kΩ)
Power supply (USB or external)

Installation & Setup
Connect all components as shown in the circuit diagram
Upload the provided Arduino sketch to your board
Calibrate the IR sensor by adjusting the threshold value if needed
Update exchange rates in the code for accurate conversions

Usage Instructions
Insert bills through the sensor mechanism
View count and total amount on the LCD

Use buttons to:
Cycle through menu options
Change display currency
Reset counters when needed

Customization Options
Add multiple sensors for different bill denominations
Integrate WiFi module for live exchange rate updates
Include EEPROM storage to maintain counts between power cycles

Add sound feedback using a buzzer

Implement thermal printing for receipts
