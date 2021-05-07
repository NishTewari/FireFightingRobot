#include <LiquidCrystal.h>    // Includes the LCD display library
#include <Servo.h>               // Includes the Servo library
Servo spiegen_servo;           // Naming the servo spigen_servo;
const int trigPin = A1;           // Setting the two pins on the arduino as the trigger and echo pin
const int echoPin = A2;
int distance;                          // int to determine the distance from the wall
long duration;                 // int to determine how long it takes for a ping to come back to the sonar
int exitRoom = 0;                  // int to determine when Spigen is leaving the room
int fourRoom = 0;                 // int to determine when Spigen needs to go to the fourth room
const int rs = 13, en = 12, d4 = 11, d5 = 10, d6 = 9, d7 = 8;   //Sets the pins on the LCD
LiquidCrystal lcd(rs, en, d4, d5, d6, d7);

void setup() {
  // put your setup code here, to run once:
  spiegen_servo.attach(A5);      // Tells what pin to attach the servo to 
  spiegen_servo.write(30);         // Turns the servo to face the right at, 30° is our 0°
  pinMode(trigPin, OUTPUT);    // Initializes trigPin to pulse out
  pinMode(echoPin, INPUT);     // Initializes echoPin to receive pulse
  pinMode (2, OUTPUT);           // Initializes pins for motors
  pinMode (3, OUTPUT);
  pinMode (4, OUTPUT);
  pinMode (5, OUTPUT);
  pinMode (6, INPUT);               // Initializes pins for line detection
  pinMode (7, INPUT);
  pinMode (A0, INPUT);             // Initializes pin to turn candle detection on
  pinMode (A3, OUTPUT);         // Initializes pin to turn fan on
  }
void loop() {
  // put your main code here, to run repeatedly:
  forward();                            // Tells Spigen to move forward
  detectWall();                       // Wall detection code running
  detectLine();                       // Line detection code running
  wallHugger();                     // Wall hugger code running
}
void halt() {                          // Tells Spigen to stop
  digitalWrite (2, LOW);
  digitalWrite (3, LOW);
  digitalWrite (4, LOW);
  digitalWrite (5, LOW);
}
void forward() {                    // Tells Spigen to move forward
  digitalWrite (2, HIGH);
  digitalWrite (3, LOW);
  digitalWrite (4, LOW);
  digitalWrite (5, HIGH);
}
void leftTurn() {                    // Tells Spigen to turn left
  digitalWrite (2, HIGH);
  digitalWrite (3, LOW);
  digitalWrite (4, LOW);
  digitalWrite (5, LOW);
}
void rightTurn() {                  // Tells Spigen to turn right
  digitalWrite (2, LOW);
  digitalWrite (3, LOW);
  digitalWrite (4, LOW);
  digitalWrite (5, HIGH);
}
void leftSpin() {                    // Tells Spigen to spin left
  digitalWrite (2, HIGH);
  digitalWrite (3, LOW);
  digitalWrite (4, HIGH);
  digitalWrite (5, LOW);
}
void rightSpin() {                  // Tells Spigen to spin right
  digitalWrite (2, LOW);
  digitalWrite (3, HIGH);
  digitalWrite (4, LOW);
  digitalWrite (5, HIGH);
}
void backwards() {              // Tells Spigen to move backwards
  digitalWrite (2, LOW);
  digitalWrite (3, HIGH);
  digitalWrite (4, HIGH);
  digitalWrite (5, LOW);
}
void detectWall() {      // Tells the sonar to send pings towards the wall and calculate the distance
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);
  duration = pulseIn(echoPin, HIGH);
  distance = (duration * 0.034) / 2;
}
void wallHugger() {            // Tells Spigen to hug the wall
  while (distance < 12) {     // If Spigen is too close to the wall he turns left
    detectWall();
    leftTurn();
    delay(20);
    forward();
    delay(10);
  }
  if (distance > 13) {           // If Spigen is too far from the wall he turns right
    rightTurn();
    delay(20);
    forward();
    delay(10);
  }
  if (distance > 25) {          // If there’s a corner, Spigen turns right, slowly around it
    rightTurn();
    delay(20);
    halt();
    delay(11 );
  }
}
void candleDetect() {
  while (analogRead(A0) <= 30) {            // Runs if the candle detection sees the candle
    forward();                                             // Spigen moves forward
    if (digitalRead(6) == HIGH) {               // If the left sensor is on white run the following code
      halt();                                                // Spigen stops
      delay(300);
      while (digitalRead(7) == LOW) {      // While right sensor is on white, turn right
        rightTurn();
      }
      for(int s2 = 0; s2 < 10000; s2++) {
      digitalWrite (A3, HIGH);                   //Turns the fan on 
      }                       
    }
    if (digitalRead(7) == HIGH) {             // If the right sensor is on white run the following code
      halt();                                              // Spigen stops
      delay(300);
      while (digitalRead(6) == LOW) {    // While left sensor is on white, turn left
        leftTurn();
      }
      for(int s2 = 0; s2 < 10000; s2++) {
      digitalWrite (A3, HIGH);          //Turns the fan on 
      }               
    }
  }
}

void detectLine() {
  if (exitRoom == 0) {                            // This code runs when entering the room
    if (digitalRead(6) == HIGH) {            // If the left sensor is on white, the following code runs
      lcd.begin(16, 2);                             // LCD turns on and prints that it is on the line
      lcd.print("Line");
      halt();                                             // Spigen stops
      delay(300);
      while (digitalRead(7) == LOW) {   // While the right sensor is on black, turn right
        rightTurn();
      }
      halt();                                              // Tells Spigen to go inside the room
      delay(300);
      forward();
      delay(1000);
      halt();
      delay(300);
      for (int spinAround = 0; spinAround <= 275; spinAround++) {    
      // Tells Spigen to spin around three times
        if (analogRead(A0) >= 30) {
          rightSpin();
          candleDetect();
          delay(10);
        }
      }
      exitRoom = 1;   // Sets exitRoom to one
      fourRoom += 1;  // Adds one to roomFour
    }
    if (digitalRead(7) == HIGH) {  // Same as above code except its if the right sensor hits the line 
      lcd.begin(16, 2);
      lcd.print("Line");
      halt();
      delay(300);
      while (digitalRead(6) == LOW) {
       leftTurn();
      }
      halt();
      delay(300);
      forward();
      delay(1000);
      halt();
      delay(300);
      for (int spinAround = 0; spinAround <= 275; spinAround++) {
        if (analogRead(A0) >= 30) {
          rightSpin();
          candleDetect();
          delay(10);
        }
      }
      exitRoom = 1;
      fourRoom += 1;
    }
  }
  if (fourRoom == 3) {                        // This is for exiting the third room and entering the fourth
    if (digitalRead(6) == HIGH) {        // This straightens Spigen on the white line before he leaves
      lcd.begin(16, 2);
      lcd.print("Line");
      halt();
      delay(300);
      while (digitalRead(7) == LOW) {
        rightTurn();
      }
      forward();                  // This tells Spigen to go forward and spin right 
      delay(850);
      halt();
      delay(300);
      rightSpin();
      delay(500);
      halt();
      delay(300);
      forward();   // Spigen goes forward on a delay to bypass wallHugger towards the fourth room 
      delay(3300);
      exitRoom = 0;      // sets exitRoom to zero
      fourRoom = 0;     // sets roomFour to zero
    }
    if (digitalRead(7) == HIGH) {      // Same as above except the right sensor hits the line first
      lcd.begin(16, 2);
      lcd.print("Line");
      halt();
      delay(300);
      while (digitalRead(6) == LOW) {
        leftTurn();
      }
      forward();
      delay(850);
      halt();
      delay(300);
      rightSpin();
      delay(500);
      halt();
      delay(300);
      forward();
      delay(3300);
      exitRoom = 0;
      fourRoom = 0;
    }
  }
  if (exitRoom == 1) {                 // Spigen does this when he exits the room
    forward();
    if (digitalRead(6) == HIGH) {  // The following if statement straightens Spigen on the white line
      lcd.begin(16, 2);
      lcd.print("Line");
      halt();
      delay(300);
      while (digitalRead(7) == LOW) {
        rightTurn();
      }
      forward();                          // Spigen goes forward and turn left to resume wall hugging code
      delay(1500);
      halt();
      delay(300);
      leftSpin();
      delay(850);
      exitRoom = 0;                    // Sets exitRoom to zero
    }
    if (digitalRead(7) == HIGH) {             // Same as above except its for the right sensor
      lcd.begin(16, 2);
      lcd.print("Line");
      halt();
      delay(300);
      while (digitalRead(6) == LOW) {
        leftTurn();
      }
      forward();
      delay(1500);
      halt();
      delay(300);
      leftSpin();
      delay(850);
      exitRoom = 0;
    }
  }
}
