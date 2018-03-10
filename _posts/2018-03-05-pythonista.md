---
title: Control de Leds con Pythonista y ESP8266
---

{% include ads.html %}
# {{ page.title }}
!hola a todos en este tutorial vamos a ver como encender y apagar leds usando pytonista y ESP8266 

![](https://i.imgur.com/JOslN4a.jpg)

## Requisitos
- Pythonista 3
- ESP8266 (en mi caso: Wemos D1 Mini)
- Leds de diferentes colores x4
- resistencias 220 ohm x4

## Esquema

![Esquema](https://i.imgur.com/f12t6my.jpg)

## Explicacion de montado 

Una vez implementado el esquema procedemos a subir el código a la ESP8266 mediante el IDE de Arduino, cuando ya este subido el código en la placa procedemos a conectarnos a la red Wifi que crea la placa ESP8266 y ponemos la contraseña de acceso que establecimos en el código del ESP8266, estando conectados a la red del ESP8266 abrimos la aplicación que hemos creado y la ejecutamos, si todo se encuentra correctamente configurado procedemos a hacer uso de la interfaz gráfica de la app para enceder y apagar los leds de manera indivudual o apargarlos todos como se muestra a continuación.

![app Pythonista](https://i.imgur.com/3Dh2ry0.jpg)

Puedes descargar el código del ESP8266 y  la app en Pythonista desde: [aqui](http://corneey.com/wmGdTz)

## Código

### Pythonista

código implementado en Pythonista 3 

```python
import ui
import socket
import time

def sw1(s): 
	tx.sendto(bytes('1', 'utf-8'), (HOST, PORT))
	
def sw2(s): 
	tx.sendto(bytes('2', 'utf-8'), (HOST, PORT))

def sw3(s): 
	tx.sendto(bytes('3', 'utf-8'), (HOST, PORT))

def sw4(s): 
	tx.sendto(bytes('4', 'utf-8'), (HOST, PORT))

def sw5(s): 
	tx.sendto(bytes('0', 'utf-8'), (HOST, PORT))

def sw6(s): 
	tx.sendto(bytes('5', 'utf-8'), (HOST, PORT))
	
HOST, PORT = '192.168.4.1', 7000
rx = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
time.sleep(1)
rx.bind(('', 9000))

tx = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
on = '#ff6161'
off = '#5d5d5d'
v = ui.load_view()
v.present('sheet')
c = True

while True:
	d1 = str(rx.recv(12), 'utf-8')
	
	if d1.find('1H') > -1:
		v['button3'].background_color = on
		
	if d1.find('1L') > -1:
		v['button3'].background_color = off
	
	if d1.find('2H') > -1:
		v['button4'].background_color = on
		
	if d1.find('2L') > -1:
		v['button4'].background_color = off
	
	if d1.find('3H') > -1:
		v['button5'].background_color = on
		
	if d1.find('3L') > -1:
		v['button5'].background_color = off
		
	if d1.find('4H') > -1:
		v['button6'].background_color = on
		
	if d1.find('4L') > -1:
		v['button6'].background_color = off
		
	d1 = ''	
```
### ESP8266

código implementado y subido a la placa mediante Arduino IDE

```C++
#include <ESP8266WiFi.h>
#include <WiFiUDP.h>


WiFiUDP UDP;                              //
char buff[32];                            //
const char ssid[]="ESP8266";              // wifi ssid
const char pass[]="yourpassword";         // wifi password
const char iPhoneIP[]   = "192.168.4.2";  // iPhone/Ipad iP
const int  iPhoneTxPort = 7000;           // UDP Tx port
const int  iPhoneRxPort = 9000;           // UDP Rx port
String all;                               //
  

 
void setup() {
 
  Serial.begin(115200);Serial.println();      
  pinMode(D0, OUTPUT); digitalWrite(D0, LOW); 
  pinMode(D1, OUTPUT); digitalWrite(D1, LOW);  
  pinMode(D2, OUTPUT); digitalWrite(D2, LOW);  
  pinMode(D3, OUTPUT); digitalWrite(D3, LOW);  
 
  WiFi.softAP(ssid, pass);                      // WiFi connect
  UDP.begin(iPhoneTxPort);                      // iPhoneTxPort
}
 
void loop() {
 
  ESP.wdtDisable();                             //
  int rx = UDP.parsePacket();                   // packet size
  if (rx){                                      // recive
    int len = UDP.read(buff, rx);               // rx lengs
    if (len > 0){                               // rx action
      buff[len] = '\0';                         // Termination char
      int c = buff[0];                          // head haracter
      if ( c == '0' ){ all_clear();}            // 0, all off
      if ( c == '1' ){ sw(D0, 1);}              // 1, LED 1 on/off
      if ( c == '2' ){ sw(D1, 2);}              // 2, LED 2 on/off
      if ( c == '3' ){ sw(D2, 3); }             // 3, LED 3 on/off
      if ( c == '4' ){ sw(D3, 4);}              // 4, LED 4 on/off
      if ( c == '5' ){ allread();}              // 5, all read
      c = '9';                                  //
    }                                           //
  }                                             //
} 

void sw(int pin, int n){                        // 
  if ( digitalRead(pin) == 0 ){                 // 
       digitalWrite(pin, HIGH);                 // SET LED ON 
       UDP.beginPacket(iPhoneIP, iPhoneRxPort); // UDP
       UDP.print( String(n) + "H");             // return LED ON 
       UDP.endPacket();                         // UDP
  } else {                                      // 
       digitalWrite(pin, LOW );                 // SET LED OFF
       UDP.beginPacket(iPhoneIP, iPhoneRxPort); // UDP
       UDP.print( String(n) + "L");             // return LED OFF 
       UDP.endPacket();                         // UDP
  }                                             //
}                                               //
 
void all_clear(){                               // 
  ESP.wdtDisable();                             //
  all = "";                                     //
  digitalWrite(D0, LOW); io(D0, 1);  
  digitalWrite(D1, LOW); io(D1, 2); 
  digitalWrite(D2, LOW); io(D2, 3);           
  digitalWrite(D3, LOW); io(D3, 4);
  UDP.beginPacket(iPhoneIP, iPhoneRxPort);      // UDP feed back
  UDP.print( all );                             // 1L2L3L4L5L6L
  UDP.endPacket();                              // UDP
}
 
void allread(){                                 //
  ESP.wdtDisable();                             //
  all = "";                                     //
  io(D0, 1);
  io(D1, 2);
  io(D2, 3);
  io(D3, 4);
  UDP.beginPacket(iPhoneIP, iPhoneRxPort);      //
  UDP.print( all );                             // 1x2x3x4x5x6x
  UDP.endPacket();                              //
}    

void io(int pin, int n){
  if ( digitalRead(pin) == 0 ){                  
    all = all + String(n) + "L";
  } else {                                      
    all = all + String(n) + "H";
  }       
  
}

```




