# Motor Control Using a Transistor

---

## Variable Declarations

```cpp
int airValue = 490;           // Sensor value when in air (completely dry = 0% moisture)
int waterValue = 195;         // Sensor value when in water (completely wet = 100% moisture)
int motorPin = 3;             // Digital pin connected to transistor base controlling motor
int moisture = 0;             // Variable to store raw analog reading from sensor (0-1023)
int moistPercent = 0;         // Variable to store calculated moisture percentage (0-100)
```

---

## Setup Function

```cpp
void setup() {                // Setup function runs once when Arduino starts
  
  pinMode(motorPin, OUTPUT);  // Configure motor pin as an output to control transistor
  Serial.begin(9600);         // Start serial communication at 9600 baud rate for debugging
}
```

---

## Main Loop Function

```cpp
void loop() {                 // Loop function runs repeatedly forever
  
  delay(500);                 // Wait 500 milliseconds (0.5 seconds) before taking reading
  
  moisture = analogRead(A0);  // Read analog value from moisture sensor on pin A0 (0-1023)
  
  // FIXED: airValue (490) maps to 0%, waterValue (195) maps to 100%
  moistPercent = map(moisture, airValue, waterValue, 0, 100); // Convert raw reading to percentage (0-100)
  
  // Constrain to 0-100% range in case sensor reads outside calibration values
  moistPercent = constrain(moistPercent, 0, 100);
  
  Serial.print("Moisture Level: "); // Print text label to serial monitor (no newline)
  Serial.print(moistPercent);        // Print moisture percentage value
  Serial.println("%");               // Print percentage symbol and newline
  
  if(moistPercent <= 50) {           // Check if moisture is 50% or less (soil is dry)
    digitalWrite(motorPin, HIGH);    // Turn motor ON by setting pin HIGH (sends current to transistor)
    Serial.println("Motor ON");      // Print status message to serial monitor
  } else {                           // If moisture is above 50% (soil is wet enough)
    digitalWrite(motorPin, LOW);     // Turn motor OFF by setting pin LOW (stops current to transistor)
    Serial.println("Motor OFF");     // Print status message to serial monitor
  }
  
  Serial.println();                  // Print blank line for readability
}
```

---

## How It Works

1. **Reads** the moisture sensor value from analog pin A0
2. **Converts** the raw value (195-490) to a percentage (0-100%)
3. **Constrains** the percentage to stay within 0-100% range
4. **Prints** the moisture level to Serial Monitor
5. **Controls** the motor:
   - **Motor ON** if moisture ≤ 50% (soil is dry)
   - **Motor OFF** if moisture > 50% (soil is wet)

---

## Calibration Values

| Condition | Sensor Value | Moisture % |
|-----------|--------------|------------|
| Dry (Air) | 490          | 0%         |
| Wet (Water) | 195        | 100%       |

---

## Expected Serial Output

```
Moisture Level: 45%
Motor ON

Moisture Level: 78%
Motor OFF
```