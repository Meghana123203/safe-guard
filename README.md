#include <ESP32Servo.h>
#include <DHT.h>  // Include the DHT sensor library

#define DHTPIN 4        // Pin where the DHT11 sensor is connected
#define DHTTYPE DHT11   // Define the type of DHT sensor

int sensorPin = 34; 
int threshold = 2000; 
int servo1Pin = 18;   
int servo2Pin = 19; 

Servo servo1;
Servo servo2;

String state;
String action;

// Initialize DHT sensor
DHT dht(DHTPIN, DHTTYPE);  // Pin connected to DHT11 sensor

void setup() {
  pinMode(sensorPin, INPUT); 
  Serial.begin(9600);
  
  servo1.attach(servo1Pin);
  servo2.attach(servo2Pin);
  
  servo1.write(0);  
  servo2.write(0);  
  
  // Initialize DHT sensor
  dht.begin();
}

void loop() {
  int sensorValue = analogRead(sensorPin);
  
  // Read temperature and humidity from the DHT11 sensor
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();  // Temperature in Celsius
  
  // If reading is unsuccessful, return NaN for error handling
  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("Failed to read from DHT sensor!");
    return;  // Exit loop if there's an error
  }

  // Control servo motors based on sensor value
  if (sensorValue < threshold) {
    state = "cool";
    action = "servo motor on"; 
    servo1.write(180);   
    servo2.write(180);     
  } else {
    state = "dry";
    action = "servo motor off"; 
    servo1.write(0);      
    servo2.write(0);   
  }

  // Output formatted data
  Serial.print("#"); // # - SOF
  Serial.print(","); // , - Separator
  Serial.print(sensorValue); // value1 (sensor value)
  Serial.print(","); // , - Separator
  Serial.print(state); // value2 (state
  Serial.print(","); // , - Separator
  Serial.print(action); // value3 (action)
  Serial.print(","); // , - Separator
  Serial.print("Temp: ");  // value4 (Temperature)
  Serial.print(temperature);  // Temperature in Celsius
  Serial.print(", "); // Separator
  Serial.print("Humidity: ");  // value5 (Humidity)
  Serial.print(humidity);  // Humidity in percentage
  Serial.print(",");
  Serial.println("~"); // ~ - EOF
  
  delay(2000);  // Wait for 2 seconds before the next loop
}
