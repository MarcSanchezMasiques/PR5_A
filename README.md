# PR5A: Procedimiento
Añadimos la libreria Wire y dentro del setup unicamente añadimos la conexión con el serial port:
```
#include <Arduino.h>
#include <Wire.h>
void setup(){
 Wire.begin();
 Serial.begin(9600);
 while (!Serial); // Leonardo: wait for serial monitor
 Serial.println("\nI2C Scanner");
}
```
En el loop añadimos una variable tipo byte para añadir en esta la dirección del dispositivo I2C y el return de wire.endTransmission(). Tambien iniciamos esto desde 0 como contador
```
void loop(){
 byte error, address;
 int nDevices;
 Serial.println("Scanning...");
 nDevices = 0;
```
Seguimos en codigo creando un bucle que se repite 127 veces para detectar el dispositivo. Seguidamente utilizamos la función anterior, wire.endTransmission() que devuelve un valor del 0 al 4, siendo estos valores los siguientes:
- 0 = exito
- 1 = datos demasiado largos para almacenar
- 2 = NACK recibido en transmisión de dirección
- 3 = NACK recibido en transmisión de datos
- 4 = Error
Una vez iniciado este loop, el dispositivo I2C no mantiene connectado a la micro a causa de la finalización de la transmisión 
```
 for(address = 1; address < 127; address++ ){
// The i2c_scanner
// the Write.endTransmisstion to see if
// a device did acknowledge to the address.
 Wire.beginTransmission(address);
 error = Wire.endTransmission();
 if (error == 0)
 {
 Serial.print("I2C device found at address 0x");
 if (address<16)
 Serial.print("0");
 Serial.print(address,HEX);
 Serial.println(" !");
 nDevices++;
 }
 else if (error==4)
 {
 Serial.print("Unknown error at address 0x");
 if (address<16)
 Serial.print("0");
 Serial.println(address,HEX);
 }
}
if (nDevices == 0)
Serial.println("No I2C devices found\n");
else
Serial.println("done\n");
delay(5000); // wait 5 seconds for next scan
}
```
En caso de no haver errores, el resultado queda de la siguiente manera, siendo en este caso el nombre del dispositivo 3C
```
Scanning...
I2C device found at address 0x3C !
done
```