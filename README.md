# ESP32 Bluetooth RC Car

This project is an ESP32 Bluetooth-controlled RC car that uses the Dabble app for control. It also includes a 3D model of the RC car that can be 3D printed. 

## Features

- **Bluetooth Control**: Uses the Dabble app to control the car via Bluetooth.
- **Dual Motor Control**: Independently controls the left and right motors for smooth and precise movement.
- **3D Printable Model**: Includes a 3D model of the RC car chassis and components.

## Requirements

- ESP32 development board
- Dabble app installed on your smartphone
- Two DC motors
- Motor driver module
- 3D printer for printing the car chassis and components
- Miscellaneous electronic components (wires, resistors, etc.)

## Getting Started

### Hardware Setup

1. **Connect the Motors**:
   - Right motor: Connect the enable pin to GPIO 22, motor pin 1 to GPIO 16, and motor pin 2 to GPIO 17.
   - Left motor: Connect the enable pin to GPIO 23, motor pin 1 to GPIO 18, and motor pin 2 to GPIO 19.

2. **ESP32 Setup**:
   - Connect the ESP32 board to your computer.
   - Install the required libraries in the Arduino IDE.

### Software Setup

1. **Install Arduino IDE**: Make sure you have the Arduino IDE installed on your computer.
2. **Install ESP32 Board**: Follow the instructions to install the ESP32 board package in the Arduino IDE.
3. **Install Dabble Library**: Install the Dabble library from the Arduino Library Manager.

### Code

```cpp
#define CUSTOM_SETTINGS
#define INCLUDE_GAMEPAD_MODULE
#include <DabbleESP32.h>

//Right motor
int enableRightMotor=22; 
int rightMotorPin1=16;
int rightMotorPin2=17;
//Left motor
int enableLeftMotor=23;
int leftMotorPin1=18;
int leftMotorPin2=19;

#define MAX_MOTOR_SPEED 255

const int PWMFreq = 1000; /* 1 KHz */
const int PWMResolution = 8;
const int rightMotorPWMSpeedChannel = 4;
const int leftMotorPWMSpeedChannel = 5;

void rotateMotor(int rightMotorSpeed, int leftMotorSpeed)
{
  if (rightMotorSpeed < 0)
  {
    digitalWrite(rightMotorPin1,LOW);
    digitalWrite(rightMotorPin2,HIGH);    
  }
  else if (rightMotorSpeed > 0)
  {
    digitalWrite(rightMotorPin1,HIGH);
    digitalWrite(rightMotorPin2,LOW);      
  }
  else
  {
    digitalWrite(rightMotorPin1,LOW);
    digitalWrite(rightMotorPin2,LOW);      
  }
  
  if (leftMotorSpeed < 0)
  {
    digitalWrite(leftMotorPin1,LOW);
    digitalWrite(leftMotorPin2,HIGH);    
  }
  else if (leftMotorSpeed > 0)
  {
    digitalWrite(leftMotorPin1,HIGH);
    digitalWrite(leftMotorPin2,LOW);      
  }
  else
  {
    digitalWrite(leftMotorPin1,LOW);
    digitalWrite(leftMotorPin2,LOW);      
  }
  
  ledcWrite(rightMotorPWMSpeedChannel, abs(rightMotorSpeed));
  ledcWrite(leftMotorPWMSpeedChannel, abs(leftMotorSpeed));  
}

void setUpPinModes()
{
  pinMode(enableRightMotor,OUTPUT);
  pinMode(rightMotorPin1,OUTPUT);
  pinMode(rightMotorPin2,OUTPUT);
  
  pinMode(enableLeftMotor,OUTPUT);
  pinMode(leftMotorPin1,OUTPUT);
  pinMode(leftMotorPin2,OUTPUT);

  //Set up PWM for speed
  ledcSetup(rightMotorPWMSpeedChannel, PWMFreq, PWMResolution);
  ledcSetup(leftMotorPWMSpeedChannel, PWMFreq, PWMResolution);
  ledcAttachPin(enableRightMotor, rightMotorPWMSpeedChannel);
  ledcAttachPin(enableLeftMotor, leftMotorPWMSpeedChannel); 

  rotateMotor(0,0); 
}

void setup()
{
  setUpPinModes();
  Dabble.begin("Z_Bluetooth_Car"); 
}

void loop()
{
  int rightMotorSpeed=0;
  int leftMotorSpeed=0;
  Dabble.processInput();
  if (GamePad.isUpPressed())
  {
    rightMotorSpeed = MAX_MOTOR_SPEED;
    leftMotorSpeed = MAX_MOTOR_SPEED;
  }

  if (GamePad.isDownPressed())
  {
    rightMotorSpeed = -MAX_MOTOR_SPEED;
    leftMotorSpeed = -MAX_MOTOR_SPEED;
  }

  if (GamePad.isLeftPressed())
  {
    rightMotorSpeed = MAX_MOTOR_SPEED;
    leftMotorSpeed = -MAX_MOTOR_SPEED;
  }

  if (GamePad.isRightPressed())
  {
    rightMotorSpeed = -MAX_MOTOR_SPEED;
    leftMotorSpeed = MAX_MOTOR_SPEED;
  }

  rotateMotor(rightMotorSpeed, leftMotorSpeed);
}
```

### 3D Model

The 3D model of the RC car can be found in the `3D_Model` folder. It includes STL files for the car chassis and other components.

## Usage

1. **Upload Code**: Upload the provided code to the ESP32 board.
2. **Connect to Dabble**: Open the Dabble app on your smartphone and connect to the ESP32 via Bluetooth.
3. **Control the Car**: Use the gamepad module in the Dabble app to control the car.

## License

This project is licensed under the GPL-3.0 License. See the [LICENSE](LICENSE) file for details.

## Acknowledgments

- Special thanks to the creators of the Dabble app and the ESP32 community for their resources and support.

---

Feel free to contribute to this project by opening issues or submitting pull requests. Happy driving!
