//Jon Hsiung
//arduino car final code 2.0
//august 2014
//digital electronics with Bruce Hubbard SU'14 ACCD

#include <Servo.h>

Servo esc;
Servo steeringServo;
const int throttlePin = 8;
const int pinMic = A3; //analog input connected to mic AUD
const int steeringServoPin = 7;
const int pinLED = 10; //digital output connected to LED
//baseline level for my mic in a fairly quiet room
//determined by watching serial monitor on first run
const int valBaseline = 340; //was 336
int steeringAngle = 90;


const int redPin = 3;
const int greenPin = 5;
const int bluePin = 6;

unsigned long clapTime = 0;
unsigned long ledTime = 0;

boolean clapDetected  = false;

// led colors
enum color_t {
  red, green, blue};
color_t ledColor = green;

// fade
const int UP = 1;
const int DOWN = 0;
int ledBrightness = 0;
int ledFade = DOWN;
int ledFadeRate = 10;

// directions
const int FWD = 2;
const int REV = 1;
int carDirection = 0;

void setup()
{
  carDirection = 0;  // unknown motion
  ledFade = DOWN; // fade down
  ledBrightness = 0; // off
  ledColor = green; //default color green

    pinMode(pinLED, OUTPUT);
  esc.attach(9);
  steeringServo.attach(steeringServoPin);

  clapTime = millis(); // read the clock
  ledTime = millis();

  pinMode(redPin,   OUTPUT); 
  pinMode(greenPin, OUTPUT);   
  pinMode(bluePin,  OUTPUT);

  digitalWrite(redPin, LOW);
  digitalWrite(greenPin, LOW);
  digitalWrite(bluePin, LOW);

}

void loop()
{
  int throttle;
  int rawThrottle;

  // every 10 ms, listen for a clap
  // sound- true if clap detected
  if ((millis() - clapTime) > 10) { // 10 milliseconds elapsed since last clap
    if (readClap (pinMic)) clapDetected = ! clapDetected; // toggles
    clapTime = millis(); // read the clock
  }

  if ((millis() - ledTime) > 5) { // 5 milliseconds elapsed since last clap
    ledTime = millis(); // read the clock
    // LED Fading
    if (carDirection == FWD) {
      // fade green up fade red down
      switch (ledColor) {
      case red: 
        if (ledBrightness >= 0) {
          ledBrightness -=ledFadeRate; // decrease
          analogWrite (redPin, max(ledBrightness, 0));
        }
        if (ledBrightness <= 0) {
          ledColor = green;
          ledBrightness = 0;
        }
        break;

      case green: 
        if (ledBrightness > 255) ledBrightness = 255;
        ledBrightness +=ledFadeRate; // increase
        analogWrite (greenPin, min(ledBrightness, 255));


        break;
      default: 
        ;
      }
    }
    else if (carDirection == REV) {
      // fade red up, fade green down
      switch (ledColor) {
      case green: 
        if (ledBrightness >= 0) {
          ledBrightness -=ledFadeRate; // decrease
          analogWrite (greenPin, max(ledBrightness,0));
        }
        if (ledBrightness <= 0) {
          ledColor = red;
          ledBrightness = 0;
        }
        break;

      case red: 
        if (ledBrightness <= 255) {
          ledBrightness +=ledFadeRate; // increase
          analogWrite (redPin, min(ledBrightness,255));
        }
        break;
      default: 
        ;
      }
    }
    else {
      analogWrite(redPin, 0);
      analogWrite(greenPin, 0);
      ledBrightness = 0;
    }
  }


  rawThrottle = analogRead(throttlePin);
  rawThrottle =constrain (rawThrottle, 10, 145);
  throttle = map(rawThrottle, 10, 115, 145, 65);

  if(clapDetected)
  {
    // sound sensor returns 10 to 315 (close to far)

    // if in reverse
    if (throttle > 91) {
      carDirection = REV;
      steeringServo.write (145);  
      esc.write(throttle);

    }

    // forward
    else if (throttle < 89) {
      carDirection = FWD;
      steeringServo.write (90);
      esc.write(throttle);
    }
    else esc.write(90); //stop
  }


  else {
    steeringServo.write (90);
    esc.write(90); //stops servo
    carDirection = 0;

  }



}

//========


int clapThreshold = 90; // was 65 for soft claps

boolean readClap (int pin) {
  boolean clapDetected = false;

  int valMic = analogRead(pin);
  int clap = abs(valMic - valBaseline);

  if (clap > clapThreshold) { 
    clapDetected = true;
    while (clap > clapThreshold-10) {
      valMic = analogRead(pin);
      clap = abs(valMic - valBaseline);
      delay(5);
    }
  }
  else {
    clapDetected = false;
  }

  if (clapDetected) digitalWrite(pinLED, HIGH);
  else digitalWrite(pinLED, LOW); 

  return clapDetected;
}





