/**************************************************************************************************************************************
  EEERover Starter Example
  
  Based on AdvancedWebServer.ino - Simple Arduino web server sample for SAMD21 running WiFiNINA shield
  For any WiFi shields, such as WiFiNINA W101, W102, W13x, or custom, such as ESP8266/ESP32-AT, Ethernet, etc
  
  WiFiWebServer is a library for the ESP32-based WiFi shields to run WebServer
  Forked and modified from ESP8266 https://github.com/esp8266/Arduino/releases
  Forked and modified from Arduino WiFiNINA library https://www.arduino.cc/en/Reference/WiFiNINA
  Built by Khoi Hoang https://github.com/khoih-prog/WiFiWebServer
  Licensed under MIT license
  
  Copyright (c) 2015, Majenko Technologies
  All rights reserved.
  
  Redistribution and use in source and binary forms, with or without modification,
  are permitted provided that the following conditions are met:
  
  Redistributions of source code must retain the above copyright notice, this
  list of conditions and the following disclaimer.
  
  Redistributions in binary form must reproduce the above copyright notice, this
  list of conditions and the following disclaimer in the documentation and/or
  other materials provided with the distribution.
  
  Neither the name of Majenko Technologies nor the names of its
  contributors may be used to endorse or promote products derived from
  this software without specific prior written permission.
  
  THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND
  ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
  WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
  DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR
  ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
  (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
  LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON
  ANY THEORY OF LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
  (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS
  SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
 ***************************************************************************************************************************************/
#define USE_WIFI_NINA         false
#define USE_WIFI101           true
#include <WiFiWebServer.h>

const char ssid[] = "EEERover";
const char pass[] = "exhibition";

//const char ssid[] = "O Dogs Handy";
//const char pass[] = "AUFH-Mt6J-MkXi-VLgQ";

//const char ssid[] = "Abby's iPhone (2)";
//const char pass[] = "therightpassword";


const int groupNumber = 20; // Set your group number to make the IP address constant - only do this on the EEERover network

//Webpage to return when root is requested
const char webpage[] = \
"<html><head><style>\
.btn {background-color: inherit;padding: 14px 28px;font-size: 16px;}\
.btn:hover {background: #eee;}\
table, th, td {\
border: 1px solid black;\
}\
</style></head>\
<body>\
<section id= \"buttons\">\
<button class=\"btn\" onclick=\"ledOn()\">LED On</button>\
<button class=\"btn\" onclick=\"ledOff()\">LED Off</button>\
<button class=\"btn\" onclick=\"data()\">table</button>\
<br>LED STATE: <span id=\"state\">OFF</span>\
</section>\
<h2>Q = STOP --- E = SCAN --- WASD = MOVE</h2>\
<h1>Alien Data</h1>\
<table id = \"tbl\">\
<tr>\
  <th>Name</th>\
  <th>Age</th>\
  <th>Magnetic</th>\
</tr>\
</table>\
<script>\
document.addEventListener('keydown', function(event) {\
var key = event.key;\
move(key)});\
</script>\
</body>\
<script>\
var xhttp = new XMLHttpRequest();\
var xhttp1 = new XMLHttpRequest();\
xhttp.onreadystatechange = function() {\
if (this.readyState == 4 && this.status == 200) {\
document.getElementById(\"state\").innerHTML = this.responseText;}};";

const char webpage1[] = \
"\
function ledOn() {xhttp.open(\"GET\", \"/on\"); xhttp.send();}\
function ledOff() {xhttp.open(\"GET\", \"/off\"); xhttp.send();}\
function wkey() {xhttp.open(\"GET\", \"/wkey\"); xhttp.send();}\
function skey() {xhttp.open(\"GET\", \"/skey\"); xhttp.send();}\
function akey() {xhttp.open(\"GET\", \"/akey\"); xhttp.send();}\
function dkey() {xhttp.open(\"GET\", \"/dkey\"); xhttp.send();}\
function qkey() {xhttp.open(\"GET\", \"/qkey\"); xhttp.send();}\
function move(key) {\
if (key === \"w\") {wkey();}\
if (key === \"s\") {skey();}\
if (key === \"a\") {akey();}\
if (key === \"d\") {dkey();}\
if (key === \"e\") {data();}\
if (key === \"q\") {qkey();}\
}\
function data() {\
var xhttp2 = new XMLHttpRequest();\
xhttp2.onreadystatechange = function() {\
if (this.readyState == 4 && this.status == 201) {\
var data = this.responseText;\
var split = data.split(\"*\");\
var age = split[1];\
var name = split[0];\
var mag = split[2];\
var table = document.getElementById(\"tbl\");\
var row = table.insertRow(1);\
var c0 = row.insertCell(0);\
var c1 = row.insertCell(1);\
var c2 = row.insertCell(2);\
c0.innerHTML = name;\
c1.innerHTML = age;\
c2.innerHTML = mag;}};\
xhttp2.open(\"GET\", \"/data\");\
xhttp2.send();}\
</script></html>";





WiFiWebServer server(80);


//Return the web page
void handleRoot()
{
  server.sendHeader("Transfer-Encoding", "chunked");
  server.sendContent(webpage);
  server.sendContent(webpage1);
  server.sendContent(F(""));
  server.client().stop();
  
}

//Switch LED on and acknowledge
void ledON()
{
  digitalWrite(LED_BUILTIN,1);
  server.send(200, F("text/plain"), F("ON"));
}

//Switch LED on and acknowledge
void ledOFF()
{
  digitalWrite(LED_BUILTIN,0);
  server.send(200, F("text/plain"), F("OFF"));
}


const int signal_pin = 6;
unsigned long high_time, low_time, tmp_age;
float age;
#define Hall_Sensor_Pin A0
int base_level, voltage, tmp_sum;
char field; // x = none n = north s = south
int offset = 2;

//change me
bool Test = false; // false for all non test purposes
//change me
void Data()
{
  
  Serial1.begin(600);
  //delay(1000);
  
  String Server_Message = "";
  if(Test == true){
    Server_Message = "john";
    Server_Message += "*117*x";
    server.send(201, F("text/plain"), F("john*117*x"));
    return;
  }

  //NAME
//  char receivedChar;
  String Recieved;
  String Recieved1;
  int timeout = 20;
  
  while (!Serial1.available() && timeout > 0){
    delay(50);
    timeout--;
  }
  Serial1.readStringUntil('#');
  Recieved = Serial1.readStringUntil('#');
  Serial.println(Recieved1); // testing

  Server_Message += Recieved;
  
  /*if (Serial1.available()){
    receivedChar = char(Serial1.read());
    
    while (receivedChar != '#')
    {
      receivedChar = char(Serial1.read());
    }
    for (int i = 0; i < 3; i++){
      Server_Message += String(char(Serial1.read()));
    }
    Serial.println(Server_Message);

  }*/


  Serial1.end();
  delay(100);
  //Serial1.begin(600);
  Serial1.readString();
  //Serial1.end();
  //Serial1.flush();

  
  //AGE

  Serial.println("age");
  age = -1;
  for (int i = 0; i < 10; i++){
    high_time = pulseIn(signal_pin, HIGH, 100000);
    low_time = pulseIn(signal_pin, LOW, 100000);
    tmp_age = (high_time + low_time) / 10;

    if ((round(tmp_age / 10.0)) != (round(age / 10.0))){
      age = (tmp_age / 10) * 10;
      i = 0;
    }
  }

  String temp = String(round(age));
  Server_Message += "*";
  Server_Message += temp;

  //MAG

  Serial.println("mag");
  for (int i = 0; i < 10; i++){
    voltage = analogRead(Hall_Sensor_Pin);
    Serial.println(voltage);
 
    if (voltage > base_level + offset){
      if (field != 'd'){
        field = 'd';
        i = 0;
      }
    }
    else if (voltage < base_level - offset){
      if (field != 'u'){
        field = 'u';
        i = 0;
      }
    }
    else{
      if (field != 'x'){
        field = 'x';
        i = 0;
      }
    }
  }
  
  temp = String(field);
  Server_Message += "*";
  Server_Message += temp;
  server.send(201, F("text/plain"), Server_Message);
  
}

int speed = 0;

void Forward()
{
  speed += 20;
  if (speed > -130 && speed < 130){ // dead zone
    speed = 140;
  }

  if (speed < 0){
    digitalWrite(8, 1);
    digitalWrite(2, 1);
  }
  else{
    digitalWrite(8, 0);
    digitalWrite(2, 0);
  }

  analogWrite(9,abs(speed));
  analogWrite(3,abs(speed));
  delay(20);
  server.send(201, F("text/plain"), F("forwards"));
}

void Backwards()
{
  speed -= 20;
  if (speed > -130 && speed < 130){ // dead zone
    speed = -140;
  }

  if (speed < 0){
    digitalWrite(8, 1);
    digitalWrite(2, 1);
  }
  else{
    digitalWrite(8, 0);
    digitalWrite(2, 0);
  }
  
  analogWrite(9,abs(speed));
  analogWrite(3,abs(speed));
  delay(20);
  server.send(201, F("text/plain"), F("back"));
}

void stop(){
  speed = 0;
  analogWrite(9,speed);
  analogWrite(3,speed);
  delay(20);
  server.send(201, F("text/plain"), F("stop"));
}

void right(){
  analogWrite(9,200);
  analogWrite(3,200);
  // left goes reverse, right goes forward
  digitalWrite(8,1);
  digitalWrite(2,0);
  delay(20);
  server.send(201, F("text/plain"), F("right"));
}

void left(){
  analogWrite(9,200);
  analogWrite(3,200);
  // left goes reverse, right goes forward
  digitalWrite(8,0);
  digitalWrite(2,1);
  delay(20);
  server.send(201, F("text/plain"), F("left"));
}

//Generate a 404 response with details of the failed request
void handleNotFound()
{
  String message = F("File Not Found\n\n"); 
  message += F("URI: ");
  message += server.uri();
  message += F("\nMethod: ");
  message += (server.method() == HTTP_GET) ? F("GET") : F("POST");
  message += F("\nArguments: ");
  message += server.args();
  message += F("\n");
  for (uint8_t i = 0; i < server.args(); i++)
  {
    message += " " + server.argName(i) + ": " + server.arg(i) + "\n";
  }
  server.send(404, F("text/plain"), message);
}

void setup()
{
  pinMode(LED_BUILTIN, OUTPUT);
  pinMode(0, INPUT);
  pinMode(1, INPUT);
  pinMode(signal_pin, INPUT);
  pinMode(Hall_Sensor_Pin,INPUT);
  pinMode(9,OUTPUT); //enable(left)
  pinMode(8,OUTPUT); //Direction(left)
  pinMode(3,OUTPUT);//enable (right)
  pinMode(2,OUTPUT);//DIRECTION (RIGHT)

  //initial state turn off motors and LED
  analogWrite(9,0);
  analogWrite(3,0);
  digitalWrite(LED_BUILTIN, 0);

  Serial.begin(9600);
  //Serial1.begin(600);

  //Wait 10s for the serial connection before proceeding
  //This ensures you can see messages from startup() on the monitor
  //Remove this for faster startup when the USB host isn't attached
  while (!Serial && millis() < 5000);  

  Serial.println(F("\nStarting Web Server"));

  //Check WiFi shield is present
  if (WiFi.status() == WL_NO_SHIELD)
  {
    Serial.println(F("WiFi shield not present"));
    while (true);
  }

  //Configure the static IP address if group number is set
  if (groupNumber)
    WiFi.config(IPAddress(192,168,0,groupNumber+1));

  // attempt to connect to WiFi network
  Serial.print(F("Connecting to WPA SSID: "));
  Serial.println(ssid);
  while (WiFi.begin(ssid, pass) != WL_CONNECTED)
  {
    delay(500);
    Serial.print('.');
  }

  //Register the callbacks to respond to HTTP requests
  server.on(F("/"), handleRoot);
  server.on(F("/on"), ledON);
  server.on(F("/off"), ledOFF);
  server.on(F("/data"), Data);
  server.on(F("/wkey"), Forward);
  server.on(F("/skey"), Backwards);
  server.on(F("/akey"), left);
  server.on(F("/dkey"), right);
  server.on(F("/qkey"), stop);


  server.onNotFound(handleNotFound);
  
  server.begin();
  
  
  Serial.print(F("HTTP server started @ "));
  Serial.println(static_cast<IPAddress>(WiFi.localIP()));

  
  tmp_sum = 0;
  for (int i = 0; i < 10; i++){
    tmp_sum = tmp_sum + analogRead(Hall_Sensor_Pin);
  }
  base_level = tmp_sum / 10;
  Serial.println(base_level);

}

//Call the server polling function in the main loop
void loop()
{
  server.handleClient();
}
