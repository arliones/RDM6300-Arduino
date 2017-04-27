# RDM6300-Arduino
Driver for RDM6300 RFID reader to Arduino. RDM6300 supports 125KHz tags that store 40-bit IDs. The driver handles the IDs as 64-bit integers (unsigned long long), keeping the 24 MSB always in zero.

The current version was tested using SoftwareSerial on Arduino Uno, and HardwareSerial for Arduino Due. Please note that there are diferences in the instantiation of the component for these boards, probably due to some problem with global constructors using the ARM compiler (I still looking into the actual causes of this issue). Also note that Arduino's Serial.print does not support 64-bit integers, so RDM6300 implements a method to print tag IDs.

## Installation

To install the library, download the zip file and import it into the Arduino environment by the "Sketch" menu in the Arduino IDE.

## Example: SoftwareSerial on Arduino Uno (AVR)

```cpp
#include <SoftwareSerial.h>
#include "RDM6300.h"

SoftwareSerial rdm_serial(2, 3);
RDM6300<SoftwareSerial> rdm(&rdm_serial);

int led_pin = 13;

void blink(int n = 1) 
{
  for(int i = 0; i < n; i++) {
    digitalWrite(led_pin, HIGH);
    delay(200);
    digitalWrite(led_pin, LOW);
    delay(200);
  }
}

void setup()
{
  pinMode(led_pin, OUTPUT);
  digitalWrite(led_pin, LOW);
  Serial.begin(115200);
  Serial.println("SETUP");
  blink(5);
}

void loop()
{
  static const unsigned long long my_id = 0x0000ABCDEF;
  static unsigned long long last_id = 0;

  last_id = rdm.read();
  Serial.print("RFID: 0x");
  rdm.print_int64(last_id);
  Serial.println();
  
  if(last_id == my_id) blink(3);

}
```

## Example: HardwareSerial on Arduino Due (ARM)

```cpp
#include "RDM6300.h"

RDM6300<HardwareSerial> * rdm;

int led_pin = 13;

void blink(int n = 1) 
{
  for(int i = 0; i < n; i++) {
    digitalWrite(led_pin, HIGH);
    delay(200);
    digitalWrite(led_pin, LOW);
    delay(200);
  }
}

void setup()
{
  rdm = new RDM6300<HardwareSerial>(&Serial1);
  pinMode(led_pin, OUTPUT);
  digitalWrite(led_pin, LOW);
  Serial.begin(115200);
  Serial.println("SETUP");
  blink(5);
}

void loop()
{
  static const unsigned long long my_id = 0x0000ABCDEF;
  static unsigned long long last_id = 0;

  last_id = rdm->read();
  Serial.print("RFID: 0x");
  rdm->print_int64(last_id);
  Serial.println();
  
  if(last_id == my_id) blink(3);

}

```
