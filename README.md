# An-IoT-Based-Door-Intrusion-Detection-System

#include <SoftwareSerial.h>
#define SIM800_TX_PIN 8
#define SIM800_RX_PIN 7
#define pirPin 5
int calibrationTime = 30;
long unsigned int lowIn;
long unsigned int pause = 5000;
boolean lockLow = true;
boolean safety = true;
boolean takeLowTime;
int PIRValue = 0;
SoftwareSerial serialSIM800(SIM800_TX_PIN, SIM800_RX_PIN);
char phone[11] = "8610933582"; // Enter your Number here.
char theftalertmessage[141] = "Theft Alert in Home";
void setup()
{
Serial.begin(9600);
Serial.println(F("Initializing Engine....(May take 10 seconds)"));
delay(10000);
serialSIM800.begin(9600);
Serial.println(F("GSM is OK"));
}
void loop()
{
if (digitalRead(pirPin) == HIGH) {
if (lockLow) {
PIRValue = 1;
lockLow = false;
}
takeLowTime = true;
}
if (digitalRead(pirPin) == LOW) {
if (takeLowTime) {
lowIn = millis();
takeLowTime = false;
}
if (!lockLow && millis() - lowIn > pause) {
PIRValue = 0;
lockLow = true;
}
}
if (PIRValue == 1) {
Serial.println("Theft Alert.");
make_multi_call();
safety = true;
} else if (safety) {
Serial.println("safe.");
safety = false;
}
}
void make_multi_call()
{
if (phone != "")
{
char end[2] = ";";
end[2] = "\0";
phone[11] = "\0";
char COMMAND[14] = "ATD";
strcat(COMMAND,phone);
strcat(COMMAND,end);
Serial.print("Phone 1: ");
make_call(COMMAND);
}
}
void make_call(String phone)
{
Serial.println("calling....");
serialSIM800.println(phone);
delay(10000); //25 sec delay
serialSIM800.println("ATH");
}
