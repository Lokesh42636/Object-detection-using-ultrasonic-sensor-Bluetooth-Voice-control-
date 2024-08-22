# Object-detection-using-ultrasonic-sensor-Bluetooth-Voice-control-
This project aims to obstacle avoidance, Bluetooth control, and voice control functions. Also, It mainly uses an Ultrasonic sensor and a Bluetooth module. 
//Code explanation
//Firstly, libraries are included.

#include <Servo.h>
#include <AFMotor.h>

//Secondly, ultrasonic sensor pins,  servo motor pins,  motor speed, and servo motor starting point are defined.

#define Echo A0
#define Trig A1
#define motor 10
#define Speed 170
#define spoint 103

//Thirdly, some variables have been created to help the program.

char value;
int distance;
int Left;
int Right;
int L = 0;
int R = 0;
int L1 = 0;
int R1 = 0;

//Then, objects are created for the Servo Library and the AFMotor Library.

Servo servo;
AF_DCMotor M1(1);
AF_DCMotor M2(2);
AF_DCMotor M3(3);
AF_DCMotor M4(4);

//In the setup function, Ultrasonic pins are set to INPUT and OUTPUT. Also, the gear motor speeds have been included.

void setup() {
  Serial.begin(9600);
  pinMode(Trig, OUTPUT);
  pinMode(Echo, INPUT);
  servo.attach(motor);
  M1.setSpeed(Speed);
  M2.setSpeed(Speed);
  M3.setSpeed(Speed);
  M4.setSpeed(Speed);
}

//In the loop function, the three main functions are included. we can run these functions one by one. These are described below.

void loop() {
  //Obstacle();
  //Bluetoothcontrol();
  //voicecontrol();
}

//This function includes the Bluetooth control code. The code lines are described one by one in the code

void Bluetoothcontrol() {
//gets the serial communication values and puts them into the char variable.
  if (Serial.available() > 0) {
    value = Serial.read();
    Serial.println(value);
  }
//Next, these values are checked using the IF condition. 
//Then, if the char value is 'F', the car moves forward. 
  if (value == 'F') {
    forward();
//If the char value is "B", the car moves backward.
  } else if (value == 'B') {
    backward();
//If the char value is "L", the car moves left.
  } else if (value == 'L') {
    left();
//If the char value is "R", the car moves right.
  } else if (value == 'R') {
    right();
//If the char value is "S", the car is stopped.
  } else if (value == 'S') {
    Stop();
  }
}

//This function includes the obstacle-avoiding code. The code lines are described one by one in the code.

void Obstacle() {

//gets the ultrasonic sensor reading and puts it into the variable.
  distance = ultrasonic();

//then, these values are checked using the IF condition.
//If the value is less than or equal to 12, 
//the robot is stopped and the servo motor rotate left and right.
// Also, gets both side distance.
  if (distance <= 12) {
    Stop();
    backward();
    delay(100);
    Stop();
    L = leftsee();
    servo.write(spoint);
    delay(800);
    R = rightsee();
    servo.write(spoint);

//After, if the left side distance less than the right side distance. The robot turns right.
    if (L < R) {
      right();
      delay(500);
      Stop();
      delay(200);

//After, if the left side distance more than the right side distance. The robot turns left.
    } else if (L > R) {
      left();
      delay(500);
      Stop();
      delay(200);
    }

//Otherwise, the robot moves forward.
  } else {
    forward();
  }
}

//This function includes the voice control code. The code lines are described one by one in the code.

void voicecontrol() {

//gets the serial communication values and puts them into the char variable.
  if (Serial.available() > 0) {
    value = Serial.read();
    Serial.println(value);

//If the char value is "^", the car moves forward.
    if (value == '^') {
      forward();

//If the char value is "-", the car moves backward.
    } else if (value == '-') {
      backward();

//If the char value is "<", the car moves left.
    } else if (value == '<') {
      L = leftsee();
      servo.write(spoint);
      if (L >= 10 ) {
        left();
        delay(500);
        Stop();
      } else if (L < 10) {
        Stop();
      }

//If the char value is ">", the car moves right.
    } else if (value == '>') {
      R = rightsee();
      servo.write(spoint);
      if (R >= 10 ) {
        right();
        delay(500);
        Stop();
      } else if (R < 10) {
        Stop();
      }

//If the char value is "*", the car is stopped.
    } else if (value == '*') {
      Stop();
    }
  }
}
