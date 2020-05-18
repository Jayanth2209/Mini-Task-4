# Project 2 : Building a Weather Station    
## Problem Statement:    
To build a Weather Station that displays weather information and predicts forthcoming weather situations         
Project Link : [Project 2 : Mini Task 2](https://github.com/Jayanth2209/Mini-Task-2/blob/master/Project%202.md)         
## Project Pipeline:      
Sensors (Temperature, Humidity and Pressure) --> NodeMCU --> IoT Cloud --> NodeMCU --> LCD Display     
So, the pipeline consists of two parts:     
* The Transmitting End : Sensors and NodeMCU - NodeMCU collects data from all the sensors and sends it to an open, online IoT platform and the data is processed       
* The Receiving End : NodeMCU and LCD Display - NodeMCU collects this processed data and drives the LCD display     
The user can also access the Weather information online, on the IoT platform from anywhere and locally, an LCD display is used to display the Weather information.      
### Transmitting End:      
Temperature (LM35), Humidity (DHT11) and Pressure (BMP180) sensors + NodeMCU        
#### LM35 Temperature Sensor:    
Connection of temperature sensor (LM35) with NodeMCU board is as follows:
* VCC pin to 3.3v of NodeMCU or can be connected to external 5V      
* GND pin to GND of NodeMCU
* Output pin to Analog pin of NodeMCU     
All these connections are ensured. So whatever the temperature of surrounding is, LM35 will read and pass it to NodeMCU through the analog pin, further the board processes that data from LM35 and sends it to the cloud (some open source IoT platform) for processing.   
##### Testing LM35:     
To check if LM35 is working or not, you can use a multimeter as a low range voltmeter and measure the output to see if it is giving you the right output voltage corresponding to the room temperature, as specified in [LM35 datasheet](http://www.ti.com/lit/ds/symlink/lm35.pdf). You can also give some of your body heat by putting a finger over the sensor and check if the output voltage increases accordingly.    
#### DHT11 Pressure Sensor:    
Connection of Humidity sensor (DHT11) with NodeMCU board is as follows:
* Pin 1 of the DHT11 goes into +3v pin of the NodeMCU.
* Pin 2 of the DHT11 goes into a digital pin of the NodeMCU.
* Pin 3 of the DHT11 goes into Ground Pin (GND) of the NodeMCU.    
These connections are ensured. DHT11 reads the humidity and sends to NodeMCU which then sends it to the cloud for processing.     
##### Testing DHT11:     
Upload the code and ensure you are getting the humidity values properly. If not, try checking the connections and code again (or) replace the sensor with a new one.     
#### BMP180 Sensor:    
Connection of Pressure sensor (BMP180) with NodeMCU board is as follows:       
* BMP180 VIN to 3.3V of NodeMCU  
* GND pin of BMP180 to GND of NodeMCU 
* SCL to D1 and SDA to D2 of NodeMCU      
All these connections are ensured. The sensor reads the pressure data and sends this data to NodeMCU which sends it to the cloud for processing.      
##### Testing BMP180:   
The following code can be used to test the working of BMP180 sensor. After uploading code open serial monitor at 115200 baud rate and you will find all sensor readings are coming. In case of any error check your connections.      
``` 
#include <SFE_BMP180.h>
#include <Wire.h>
// You will need to create an SFE_BMP180 object, here called "pressure":
SFE_BMP180 pressure;
 #define ALTITUDE 1655.0 // Altitude in meters
void setup()
{
  Serial.begin(115200);
  Serial.println("REBOOT: circuits4you.com");
  // Initialize the sensor (it is important to get calibration values stored on the device).
   if (pressure.begin())
    Serial.println("BMP180 init success");
  else
  {
    Serial.println("BMP180 init fail\n\n");
    while(1); // Pause forever.
  }
}
 void loop()
{
  char status;
  double T,P,p0,a;
  // Loop here getting pressure readings every 10 seconds.
  // If you want sea-level-compensated pressure, as used in weather reports,
  // you will need to know the altitude at which your measurements are taken.
  // We're using a constant called ALTITUDE in this sketch:
   Serial.println();
  Serial.print("provided altitude: ");
  Serial.print(ALTITUDE,0);
  Serial.print(" meters, ");
  Serial.print(ALTITUDE*3.28084,0);
  Serial.println(" feet");
  // If you want to measure altitude, and not pressure, you will instead need
  // to provide a known baseline pressure. This is shown at the end of the sketch.
 // You must first get a temperature measurement to perform a pressure reading.
   // Start a temperature measurement:
  // If request is successful, the number of ms to wait is returned.
  // If request is unsuccessful, 0 is returned.
 status = pressure.startTemperature();
  if (status != 0)
  {
    // Wait for the measurement to complete:
    delay(status);
    // Retrieve the completed temperature measurement:
    // Note that the measurement is stored in the variable T.
    // Function returns 1 if successful, 0 if failure.
  status = pressure.getTemperature(T);
   if (status != 0)
    {
      // Print out the measurement:
     Serial.print("temperature: ");
      Serial.print(T,2);
      Serial.print(" deg C, ");
      Serial.print((9.0/5.0)*T+32.0,2);
      Serial.println(" deg F");
      // Start a pressure measurement:
      // The parameter is the oversampling setting, from 0 to 3 (highest res, longest wait).
      // If request is successful, the number of ms to wait is returned.
      // If request is unsuccessful, 0 is returned.
       status = pressure.startPressure(3);
      if (status != 0)
      {
        // Wait for the measurement to complete:
        delay(status);
        // Retrieve the completed pressure measurement:
        // Note that the measurement is stored in the variable P.
        // Note also that the function requires the previous temperature measurement (T).
        // (If temperature is stable, you can do one temperature measurement for a number of pressure measurements.)
        // Function returns 1 if successful, 0 if failure.
        status = pressure.getPressure(P,T);
        if (status != 0)
        {
          // Print out the measurement:
          Serial.print("absolute pressure: ");
          Serial.print(P,2);
          Serial.print(" mb, ");
          Serial.print(P*0.0295333727,2);
          Serial.println(" inHg");
          // The pressure sensor returns absolute pressure, which varies with altitude.
          // To remove the effects of altitude, use the sea level function and your current altitude.
          // This number is commonly used in weather reports.
          // Parameters: P = absolute pressure in mb, ALTITUDE = current altitude in m.
          // Result: p0 = sea-level compensated pressure in mb
          p0 = pressure.sealevel(P,ALTITUDE); // we're at 1655 meters (Boulder, CO)
          Serial.print("relative (sea-level) pressure: ");
          Serial.print(p0,2);
          Serial.print(" mb, ");
          Serial.print(p0*0.0295333727,2);
          Serial.println(" inHg");
          // On the other hand, if you want to determine your altitude from the pressure reading,
          // use the altitude function along with a baseline pressure (sea-level or other).
          // Parameters: P = absolute pressure in mb, p0 = baseline pressure in mb.
          // Result: a = altitude in m.
          a = pressure.altitude(P,p0);
          Serial.print("computed altitude: ");
          Serial.print(a,0);
          Serial.print(" meters, ");
          Serial.print(a*3.28084,0);
          Serial.println(" feet");
        }
        else Serial.println("error retrieving pressure measurement\n");
      }
      else Serial.println("error starting pressure measurement\n");
    }
    else Serial.println("error retrieving temperature measurement\n");
  }
  else Serial.println("error starting temperature measurement\n");
  delay(5000);  // Pause for 5 seconds.
}     
```   
If you are not getting the data properly, try replacing the sensor with a new one.      

After making all the connections, of all sensors with NodeMCU, check if everything lights up. If not, check for any damage or check if the board is heated up. If it is burnt/damaged, replace it with a new one.     
### Receiving end:    
NodeMCU + LCD Display     
All we have to do here is to check if the connection of NodeMCU and the LCD display is proper.      
* Connect the GND pin on the LCD display to one of the GND pins on the NodeMCU.
* Connect the VCC pin on the LCD display to the VIN pin on the NodeMCU (The VIN pin on the NodeMCU is tied directly to the 5V pin on the incoming USB port. If you plan on powering the NodeMCU with something other than USB, you'll have to find another way to provide 5V to the display)
* Connect the SDA pin on the LCD display to the D2 pin on the NodeMCU.
* Connect the SDL pin on the LCD display to the D1 pin on the NodeMCU.         
Also ensure that the connection of NodeMCU and the cloud is established, and that it is reading data from the cloud. Finally, check your code for any errors.      
### Resources:
[Interfacing LM35 with NodeMCU](https://www.hackster.io/pankaj6/nodemcu-connecting-lm35-based-temperature-monitoring-system-0e56ae)     
[Interfacing BMP180 with NodeMCU](https://circuits4you.com/2019/03/23/esp8266-bmp180-pressure-sensor-interface/)     
[Interfacing DHT11 with NodeMCU](https://www.instructables.com/id/Interface-DHT11-Humidity-Sensor-Using-NodeMCU/)      
[Testing LM35](http://embedded-lab.com/blog/testing-active-analog-temperature-sensors-with-a-multimeter/)       
[LM35 Temperature Sensor](https://robu.in/product/lm35-to-92-3-board-mount-temperature-sensors/)       
[DHT11 Humidity Sensor](https://robu.in/product/dht11-digital-relative-humidity-temperature-sensor-module/)       
[BMP180 Pressure Sensor](https://robu.in/product/bmp180-digital-barometric-pressure-sensor-board-module-arduino-compatible-2/)        
[NodeMCU](https://thinkrobotics.in/products/nodemcu-v3-lua-wifi-internet-of-things-development-board?variant=16350759419976&currency=INR&gclid=EAIaIQobChMI6tnmuaG96QIVBzdgCh0h4AGkEAQYAiABEgIo7fD_BwE)      
[LCD Display](https://robu.in/product/basic-16x2-character-lcd-white-on-blue-on-blue-5v/?gclid=EAIaIQobChMI8_zg6qK96QIVylBgCh0K8ghKEAQYASABEgJqifD_BwE)      

