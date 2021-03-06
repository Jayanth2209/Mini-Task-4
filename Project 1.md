# Project 1 : Gesture Controlled Wheelchair        
## Problem Statement: 
To develop a prototype for a robotic wheelchair that can be controlled using simple hand gestures.Each hand gesture is used for a specific movement of the wheelchair      
Project Link : [Project 1 : Mini Task 2](https://github.com/Jayanth2209/Mini-Task-2/blob/master/Project%201.md)       
## Project Pipeline:     
Hand Movement/Gesture detection (Accelerometer + Gyroscope) --> Microcontroller (Arduino Nano - Transmitting End-Hand module) --> Wireless Communication Module (RF Module/Bluetooth Module) --> Microcontroller (Arduino Nano - Receiving End-Wheelchair module) --> Motor driver --> Motors (Wheelchair movements)        

So, we can succinctly put this as : 
Hand Module --> Wireless Communication Module --> Wheelchair Module       
### Hand Module : 
Consists of the Accelerometer+Gyroscope and Arduino Nano        
#### Testing Accelerometer+Gyroscope: 
An MPU 6050 is used - MEMS 3-axis accelerometer and 3-axis gyroscope      
##### Interfacing MPU 6050 and Arduino:      
MPU 6050 supports only I2C communication and hence the I2C pins of Arduino are multiplexed with the analog input pins A4 and A5 i.e. A4 is SDA and A5 is SCL. We use the SCL, SDA and the INT pins of MPU 6050 to connect with the Arduino.    
The connections are tested :   
* SCL Pin of the Arduino (A5) to SCL Pin of MPU 6050      
* SDA Pin of the Arduino (A4) to SDA Pin of MPU 6050    
* INT Pin of MPU 6050 to external input 0 (INT0) pin of Arduino i.e. Pin 2      
* GND of MPU 6050 to GND of Arduino 
* VCC of MPU 6050 to 5V Power Pin of Arduino Nano     
Also check if the power supply is proper and connected then check if all the modules light up. If not, check if some module/Arduino board is hot. If it is, it might be damaged/burnt, and we have to replace it with a new one.       
##### Uploading the Code to Arduino and Testing MPU6050:   
We need to download I2Cdev and MPU6050 libraries for Arduino. We need to download and paste these folders in the libraries folder of Arduino. Then open Arduino IDE and navigate : File -> Examples -> MPU6050 -> Examples -> MPU6050_DMP6      
Upload this code after properly checking the connections and after uploading the code, open the serial terminal. Set the baud rate to 115200 and it will display    
```
Initializing DMP...
Initializing I2C devices...
Testing device connections...
MPU6050 connection successful 
```  
If you don't get any data or if you are getting garbage data, just reset the Arduino. If you look at the last sentence, it says "Send any character to begin DMP programming and demo". So, type any character in the serial terminal and hit send. You can now start seeing the values in the serial monitor.    
###### Caution : During this time, keep the MPU6050 stable and wait a few seconds for the values from MPU6050 to be stabilized.     
### Wireless Communication Module:     
We can use an **RF Communication Module (or) a Bluetooth Module** for this purpose as discussed in Mini task 2.       
#### Testing RF Communication Module:    
This is one of the easiest and cheapest ways to implement wireless communication between two Arduino Modules.     
The RF Transmitter Module consists of 4 – pins: VCC, GND, Data and Antenna. VCC and GND pins are connected to power supply and ground respectively. The data pin is connected to any of the digital I/O pin of Arduino. The antenna pin must be connected to an antenna which is nothing but a wire wound in the form of a coil.      
The RF Receiver Module consists of 4 – pins: VCC, GND, Data and Antenna. VCC and GND pins are connected to 3.3V pin of the other Arduino and ground respectively. The antenna pin must be connected to an antenna similar to the one in transmitter module.    
All the connections are tested and power supply is ensured. We must again check if all the modules light up and also if any module is damaged/burnt. Then we can proceed to the next step.    
Refer to this link for an RF Module-Arduino interface tutorial : [Interfacing RF Module-Arduino and Testing](https://lastminuteengineers.com/433mhz-rf-wireless-arduino-tutorial/)          
#### Testing Bluetooth Module: 
We check all the connections and also ensure that the module lights up.    
Refer to this link on "How to setup and test HC-05 Bluetooth Module" : [Setup and Test Bluetooth Module with Arduino](https://www.instructables.com/id/How-to-Set-Up-and-Test-Arduino-Bluetooth-Connectio/)         
Ensure the power supply all the time and also check for any damage in the components/modules.     
Also ensure that the Arduino board is working and is not heated up i.e. damaged/burnt. Then we can proceed to the next step.     
### Wheelchair Module:    
The Wheelchair module consists of the motor drivers and motors. Make sure all the connections are proper and ensure that the motor driver is not shorted, because it can be destroyed easily. Also check the connections to the motors and ensure that there aren't any loose connections. If there are any loose connections, the motors will not work. We use the following code to test the motor driver
```
int l1 = 6;
int r1 = 5;
int l2 = 4;
int r2 = 3;

void setup() {
  
  delay(500);
  Serial.begin(9600);
  pinMode(l1, OUTPUT);
  pinMode(l2, OUTPUT);
  pinMode(r1, OUTPUT);
  pinMode(r2, OUTPUT);
  digitalWrite(l1, HIGH);
  digitalWrite(r1, HIGH);
  digitalWrite(l2, LOW);
  digitalWrite(r2, LOW);
}

void loop() {
}     
```      
If we see any movement, it means that the motor driver is working properly. Also make sure the wires touch the metal contact in the motors or any loose connections as mentioned above. We finally check the code and correct any errors, if any.      
### Resources:    
[Interfacing RF Module-Arduino and Testing](https://lastminuteengineers.com/433mhz-rf-wireless-arduino-tutorial/)        
[Setup and Test Bluetooth Module with Arduino](https://www.instructables.com/id/How-to-Set-Up-and-Test-Arduino-Bluetooth-Connectio/)     
[Arduino Nano](https://www.amazon.in/easy-electronics-Arduino-Nano-Cable/dp/B07C8G4N6X/ref=sr_1_1?dchild=1&keywords=arduino+nano&qid=1589796132&sr=8-1)        
[Arduino Uno](https://www.amazon.in/Uno-ATmega328P-Compatible-ATMEGA16U2-Arduino/dp/B015C7SC5U/ref=sr_1_2?dchild=1&keywords=arduino+uno&qid=1589796339&s=computers&sr=1-2)      
[L293D Motor Driver](https://www.amazon.in/Adraxx-L293D-Motor-Driver/dp/B01NAJHRJ0/ref=sr_1_10?keywords=L293d&qid=1568454815&s=gateway&sr=8-10)      
[HC-05 Bluetooth Module](https://www.amazon.in/HC-05-Bluetooth-Module-10g/dp/B00X86U4RW/ref=sr_1_3?keywords=Hc+-+05&qid=1568454849&s=gateway&sr=8-3)      
[RF Transmitter-Receiver Module](https://robu.in/product/rf-transmitter-receiver-module-315mhz-wireless-link-kit-for-arduino/?gclid=EAIaIQobChMI59Ts9JW96QIVFz5gCh3vMQxdEAYYAiABEgJtIvD_BwE)      
[MPU-6050](https://robu.in/product/mpu-6050-gyro-sensor-2-accelerometer/)        





