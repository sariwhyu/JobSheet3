# SistemEmbedded
### Disusun Oleh
- Sari Wahyuningsih 4.31.20.0.23

# Daftar Laporan JobSheet Sistem Embedded

- [JobSheet 1 DASAR PEMROGRAMAN ESP32 UNTUK PEMROSESAN DATA INPUT/OUTPUT ANALOG DAN DIGITAL](https://github.com/sariwhyu/JobSheet1)
- [JobSheet 1.1 JARINGAN SENSOR NIRKABEL MENGGUNAKAN ESP-NOW](https://github.com/sariwhyu/JobSheet1.1)
- [JobSheet 2 PROTOKOL KOMUNIKASI DAN SENSOR](https://github.com/sariwhyu/JobSheet2)
- [JobSheet 3 TOPOLOGI JARINGAN LOKAL DAN WIFI](https://github.com/sariwhyu/JobSheet3)
- [Tugas Nomor 1 Cayenne(MQTT)+Sensor+Button+Website Monitoring](https://github.com/sariwhyu/TugasNo1)
- [Tugas Nomor 2 Adafruit.IO+Sensor+LED+Suara](https://github.com/sariwhyu/TugasNo2)
- [Tugas Nomor 3 ThingSpeak+Sensor](https://github.com/sariwhyu/TugasNo3)
- [Tugas Nomor 4 ESP Now+IOT](https://github.com/sariwhyu/TugasNo4)

# JobSheet 3 TOPOLOGI JARINGAN LOKAL DAN WIFI

## Alat & Bahan

-	ESP32
-	Breadboard
-	Kabel jumper
-	Sensor DHT 11, RFID
-	LED (5) dan Push Button (3)
-	Servo
-	Resistor 330,1K, 10K Ohm (@ 3)


## Coding

### ESP32 Wi-Fi Modes dan Wifi-Scan

```bash
  / #include "WiFi.h"
void setup() {
Serial.begin(115200);
// Set WiFi to station mode and disconnect from an AP if it was previously connected
WiFi.mode(WIFI_STA);
WiFi.disconnect();
delay(100);
Serial.println("Setup done");
}
void loop() {
Serial.println("scan start");
// WiFi.scanNetworks will return the number of networks found
int n = WiFi.scanNetworks();
Serial.println("scan done");
if (n == 0) {
Serial.println("no networks found");
} else {
Serial.print(n);
Serial.println(" networks found");
for (int i = 0; i < n; ++i) {
// Print SSID and RSSI for each network found
Serial.print(i + 1);
Serial.print(": ");
Serial.print(WiFi.SSID(i));
Serial.print(" (");
Serial.print(WiFi.RSSI(i));
Serial.print(")");
Serial.println((WiFi.encryptionType(i) == WIFI_AUTH_OPEN)?" ":"*");
delay(10);
}
}
Serial.println("");
// Wait a bit before scanning again
delay(5000);
}

```

### Menghubungkan ESP32 dengan Jaringan Wi-Fi

```bash
  #include <WiFi.h>
// Replace with your network credentials (STATION)
const char* ssid = "sariw";
const char* password = "020501sari";
void initWiFi() {
WiFi.mode(WIFI_STA);
WiFi.begin(ssid, password);
Serial.print("Connecting to WiFi ..");
while (WiFi.status() != WL_CONNECTED) {
Serial.print('.');
delay(1000);
}
Serial.println(WiFi.localIP());
}
void setup() {
Serial.begin(115200);
initWiFi();
Serial.print("RRSI: ");
Serial.println(WiFi.RSSI());
}
void loop() {
// put your main code here, to run repeatedly:
}

```

### Menghubungkan Kembali (Re-connect) ESP32 dengan Jaringan Wi-Fi

```bash
  #include <WiFi.h>
// Replace with your network credentials (STATION)
const char* ssid = "sariw";
const char* password = "020501sari";
unsigned long previousMillis = 0;
unsigned long interval = 30000;
void initWiFi() {
WiFi.mode(WIFI_STA);
WiFi.begin(ssid, password);
Serial.print("Connecting to WiFi ..");
while (WiFi.status() != WL_CONNECTED) {
Serial.print('.');
delay(100);
}
Serial.println(WiFi.localIP());
}
void setup() {
Serial.begin(115200);
initWiFi();
Serial.print("RRSI: ");
Serial.println(WiFi.RSSI());
}
void loop() {
unsigned long currentMillis = millis();
// if WiFi is down, try reconnecting every CHECK_WIFI_TIME seconds
if ((WiFi.status() != WL_CONNECTED) && (currentMillis - previousMillis >=interval)) {
Serial.print(millis());
Serial.println("Reconnecting to WiFi...");
WiFi.disconnect();
WiFi.reconnect();
previousMillis = currentMillis;
}
}

```

### Mengganti Hostname ESP32

```bash
  #include <WiFi.h>
// Replace with your network credentials (STATION)
const char* ssid = "sariw";
const char* password = "020501sari";
String hostname = "ESP32 Node Temperature";
void initWiFi() {
WiFi.mode(WIFI_STA);
WiFi.config(INADDR_NONE, INADDR_NONE, INADDR_NONE, INADDR_NONE);
WiFi.setHostname(hostname.c_str()); //define hostname
//wifi_station_set_hostname( hostname.c_str() );
WiFi.begin(ssid, password);
Serial.print("Connecting to WiFi ..");
while (WiFi.status() != WL_CONNECTED) {
Serial.print('.');
delay(1000);
}
Serial.println(WiFi.localIP());
}
void setup() {
Serial.begin(115200);
initWiFi();
Serial.print("RRSI: ");
Serial.println(WiFi.RSSI());
}
void loop() {
// put your main code here, to run repeatedly:
}

```

### Mengirim Data Sensor ke Database

```bash
  // Import required libraries
#include "WiFi.h"
#include "ESPAsyncWebServer.h"
#include <Adafruit_Sensor.h>
#include <DHT.h>
// Replace with your network credentials
const char* ssid = "sariw";
const char* password = "020501sari";
#define DHTPIN 4 // Digital pin connected to the DHT sensor
// Uncomment the type of sensor in use:
#define DHTTYPE DHT11 // DHT 11
DHT dht(DHTPIN, DHTTYPE);
// Create AsyncWebServer object on port 80
AsyncWebServer server(80);
String readDHTTemperature() {
// Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
// Read temperature as Celsius (the default)
float t = dht.readTemperature();
// Read temperature as Fahrenheit (isFahrenheit = true)
//float t = dht.readTemperature(true);
// Check if any reads failed and exit early (to try again).
if (isnan(t)) {
Serial.println("Failed to read from DHT sensor!");
return "--";
}
else {
Serial.println(t);
return String(t);
}
}
String readDHTHumidity() {
// Sensor readings may also be up to 2 seconds 'old' (its a very slow sensor)
float h = dht.readHumidity();
if (isnan(h)) {
Serial.println("Failed to read from DHT sensor!");
return "--";
}
else {
Serial.println(h);
return String(h);
}
}
const char index_html[] PROGMEM = R"rawliteral(
<!DOCTYPE HTML><html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1">
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.7.2/css/all.css" integrity="sha384-fnmOCqbTlWIlj8LyTjo7mOUStjsKC4pOpQbqyi7RrhN7udi9RwhKkMHpvLbHG9Sr" crossorigin="anonymous">
<style>
html {
font-family: Arial;
display: inline-block;
margin: 0px auto;
text-align: center;
}
h2 { font-size: 3.0rem; }
p { font-size: 3.0rem; }
.units { font-size: 1.2rem; }
.dht-labels{
font-size: 1.5rem;
vertical-align:middle;
padding-bottom: 15px;
}
</style>
</head>
<body>
<h2>ESP32 DHT Server</h2>
<p>
<i class="fas fa-thermometer-half" style="color:#059e8a;"></i>
<span class="dht-labels">Temperature</span>
<span id="temperature">%TEMPERATURE%</span>
<sup class="units">&deg;C</sup>
</p>
<p>
<i class="fas fa-tint" style="color:#00add6;"></i>
<span class="dht-labels">Humidity</span>
<span id="humidity">%HUMIDITY%</span>
<sup class="units">&percnt;</sup>
</p>
</body>
<script>
setInterval(function ( ) {
var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
if (this.readyState == 4 && this.status == 200) {
document.getElementById("temperature").innerHTML = this.responseText;
}
};
xhttp.open("GET", "/temperature", true);
xhttp.send();
}, 10000 ) ;
setInterval(function ( ) {
var xhttp = new XMLHttpRequest();
xhttp.onreadystatechange = function() {
if (this.readyState == 4 && this.status == 200) {
document.getElementById("humidity").innerHTML = this.responseText;
}
};
xhttp.open("GET", "/humidity", true);
xhttp.send();
}, 10000 ) ;
</script>
</html>)rawliteral";
// Replaces placeholder with DHT values
String processor(const String& var){
//Serial.println(var);
if(var == "TEMPERATURE"){
return readDHTTemperature();
}
else if(var == "HUMIDITY"){
return readDHTHumidity();
}
return String();
}
void setup(){
// Serial port for debugging purposes
Serial.begin(115200);
dht.begin();
// Connect to Wi-Fi
WiFi.begin(ssid, password);
while (WiFi.status() != WL_CONNECTED) {
delay(1000);
Serial.println("Connecting to WiFi..");
}
// Print ESP32 Local IP Address
Serial.println(WiFi.localIP());
// Route for root / web page
server.on("/", HTTP_GET, [](AsyncWebServerRequest *request){
request->send_P(200, "text/html", index_html, processor);
});
server.on("/temperature", HTTP_GET, [](AsyncWebServerRequest *request){
request->send_P(200, "text/plain", readDHTTemperature().c_str());
});
server.on("/humidity", HTTP_GET, [](AsyncWebServerRequest *request){
request->send_P(200, "text/plain", readDHTHumidity().c_str());
});
// Start server
server.begin();
}
void loop(){
}

```

## Hasil

[Documentation](https://github.com/sariwhyu/JobSheet3/blob/main/COM3%202022-11-28%2010-17-07.mp4)

![App Screenshot](https://github.com/sariwhyu/JobSheet3/blob/main/1.png)

![App Screenshot](https://github.com/sariwhyu/JobSheet3/blob/main/3%20(1).png)

![App Screenshot](https://github.com/sariwhyu/JobSheet3/blob/main/3.png)

![App Screenshot](https://github.com/sariwhyu/JobSheet3/blob/main/WhatsApp%20Image%202022-11-28%20at%2011.30.18.jpeg)
