# UART-Transmission-Code
#include <EEPROM.h>

const int baudRate = 2400;
const int eepromSize = 1000;
const int bufferSize = 32; // Adjust buffer size based on your MCU's capabilities
char buffer[bufferSize];
unsigned long startTime, endTime;

void setup() 
{
  Serial.begin(baudRate);
}

void loop()
{
  // Receiving data from PC to MCU
  int index = 0;
  startTime = micros(); // Start measuring time

  while (index < eepromSize) 
  {
    if (Serial.available() > 0)
    {
      buffer[index % bufferSize] = Serial.read();
      EEPROM.write(index, buffer[index % bufferSize]);
      index++;
    }
  }

  endTime = micros(); // Stop measuring time
  printTransmissionSpeed(index, endTime - startTime);

  // Transmitting data from MCU to PC
  for (int i = 0; i < eepromSize; i++)
  {
    char data = EEPROM.read(i);
    Serial.write(data);
  }

  delay(1000); // Add delay to see the speed on PC side
}

void printTransmissionSpeed(int dataSize, unsigned long elapsedTime)
{
  float speed = (float)dataSize * 8 / (float)elapsedTime; // bits per second
  Serial.print("Transmission Speed: ");
  Serial.print(speed, 2);
  Serial.println(" bits/second");
}
