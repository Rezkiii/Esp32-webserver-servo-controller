#include <WiFi.h>
#include <ESPAsyncWebServer.h>
#include <ESP32Servo.h>

// Replace with your network credentials
const char* ssid = "ssid";
const char* password = "password";

// Servo pins
static const int servoPin1 = 13;
static const int servoPin2 = 14;

// Servo objects
Servo servo1;
Servo servo2;

// HTML page with sliders
const char* indexHTML = R"rawliteral(
<!DOCTYPE html>
<html>
<head>
  <title>ESP32 Dual Servo Control</title>
  <style>
    body { font-family: Arial; text-align: center; margin: 20px; }
    .slider-container { margin: 20px; }
  </style>
</head>
<body>
  <h1>ESP32 Dual Servo Control</h1>
  <div class="slider-container">
    <p>Servo 1 Position: <span id="servo1Value">90</span></p>
    <input type="range" id="slider1" min="0" max="180" value="90" oninput="updateServo(1, this.value)">
  </div>
  <div class="slider-container">
    <p>Servo 2 Position: <span id="servo2Value">90</span></p>
    <input type="range" id="slider2" min="0" max="180" value="90" oninput="updateServo(2, this.value)">
  </div>
  <script>
    function updateServo(servo, value) {
      document.getElementById(`servo${servo}Value`).innerText = value;
      fetch(`/slider?servo=${servo}&value=${value}`);
    }
  </script>
</body>
</html>
)rawliteral";

// Create an AsyncWebServer object on port 80
AsyncWebServer server(80);

// Current positions of the servos
int servo1Position = 90;
int servo2Position = 90;

void setup() {
  // Start serial communication
  Serial.begin(115200);

  // Attach the servos
  servo1.attach(servoPin1);
  servo2.attach(servoPin2);
  servo1.write(servo1Position);
  servo2.write(servo2Position);

  // Connect to Wi-Fi
  WiFi.begin(ssid, password);
  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.println("Connecting to WiFi...");
  }
  Serial.println("Connected to WiFi");
  Serial.println(WiFi.localIP());

  // Serve the HTML page
  server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
    request->send(200, "text/html", indexHTML);
  });

  // Handle slider values
  server.on("/slider", HTTP_GET, [](AsyncWebServerRequest *request){
    if (request->hasParam("servo") && request->hasParam("value")) {
      String servo = request->getParam("servo")->value();
      String value = request->getParam("value")->value();
      int position = value.toInt();

      if (servo == "1") {
        servo1Position = position;
        servo1.write(servo1Position);
        Serial.println("Servo 1 position: " + String(servo1Position));
      } else if (servo == "2") {
        servo2Position = position;
        servo2.write(servo2Position);
        Serial.println("Servo 2 position: " + String(servo2Position));
      }
    }
    request->send(200, "text/plain", "OK");
  });

  // Start server
  server.begin();
}

void loop() {
  // Nothing here, all handled by the server
}
