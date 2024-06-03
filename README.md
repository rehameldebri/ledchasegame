# ENGR103final
Final project demo

A bit about me..
I am an ECE major and I want to get an A in ENGR103

I made an LED chase game.
When user presses the left button, the game starts. Led lights will take turns lighting up from 0 to 9. The user should press the right button to stop the led in the right place (the led located above the 3.3v). if they press the right button and get it in the right place, the game will print “you win” and start again but this time going faster. It will do that a max of 21 times or until the user gets it wrong. If the user presses the right button when it is in the wrong place, the game will stop, make a noise, and print “you lose”. it will restart again if you press the left button
#include <Adafruit_CircuitPlayground.h>
#include <Wire.h>
#include <SPI.h>
int currentState = 1; 
const int targetPosition = 5;  // target position
int currentPosition = 0;
unsigned long lastUpdateTime;  // Should be unsigned long for millis()
int chaseInterval = 100;  // Initial LED chase speed in milliseconds
const float speedUpFactor = 0.9;  // Decrease chase interval by 10% on each win
const int minChaseInterval = 10;  // Minimum interval 
int score=0;
void setup() {
    Serial.begin(9600);
    CircuitPlayground.begin();
}

void loop() {
    switch (currentState) {
        case 1:  // IDLE
            if (CircuitPlayground.leftButton()) {
                currentPosition = 0;
                lastUpdateTime = millis();
                currentState = 2;  // Transition to CHASE state
            }
            break;

   case 2:  
            if (millis() - lastUpdateTime >= chaseInterval) { //millis() used from in class rainbowcycle example
                CircuitPlayground.clearPixels();
                CircuitPlayground.setPixelColor(currentPosition, 255, 0, 0);  
                currentPosition = (currentPosition + 1) % 10;// used from an online example
                lastUpdateTime = millis();
            }
           
   if (CircuitPlayground.rightButton()) {
              const uint8_t spGO[]         
                 PROGMEM = {0x06,0x08,0xDA,0x75,0xB5,0x8D,0x87,0x4B,0x4B,0xBA,0x5B,0xDD,0xE2,0xE4,0x49,0x4E,0xA6,0x73,0xBE,0x9B,0xEF,0x62,0x37,0xBB,0x9B,0x4B,0xDB,0x82,0x1A,0x5F,0xC1,0x7C,0x79,0xF7,0xA7,0xBF,0xFE,0x1F};
     currentState = 3;  // Transition to STOP state
            }
            break;

case 3:  
            if (currentPosition == targetPosition) {
                CircuitPlayground.setPixelColor(0, 0, 255, 0);  // Green LED for win
                Serial.println("You win!");
                score++;  
                Serial.print("Score: ");
                Serial.println(score);  
                chaseInterval = (int)(chaseInterval * speedUpFactor);  // Increase speed
                if (chaseInterval < minChaseInterval) {  // last interval after 21 trys
                    chaseInterval = minChaseInterval;
                }
                delay(1000);  
                currentPosition = 0;  
                lastUpdateTime = millis();  
                currentState = 2;  // Restart game at faster speed
            } else {
                CircuitPlayground.setPixelColor(currentPosition, 255, 0, 0);  
                CircuitPlayground.playTone(440, 500);  // Play sound for 500ms
                Serial.println("You lose!");
                delay(1000);  // Display you lose for 1 second
                currentState = 4;
            }
            break;

  case 4:  
            CircuitPlayground.clearPixels();
            chaseInterval = 100;  // Reset chase interval to initial speed
            currentState = 1;  // Transition back to IDLE state
            break;
    }
}

