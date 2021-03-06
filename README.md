#

![simplinnovation](https://4.bp.blogspot.com/-f7YxPyqHAzY/WJ6VnkvE0SI/AAAAAAAADTQ/0tDQPTrVrtMAFT-q-1-3ktUQT5Il9FGdQCLcB/s350/simpLINnovation1a.png)

# Bluetooth Running Text 1.0

### __Bluetooth Running Text 1.0__ is a simple Arduino running text project that can be controlled over bluetooth. You can simply set the text that will be displayed on your own running text, by using Android phone. Watch the video below ([click here](https://www.youtube.com/watch?v=e0RuGO_gGvA)) to see its action, then follow the instructions below to build your own Bluetooth Running Text!

#

[![Video Lintang Bluetooth Running Text 1.0](https://img.youtube.com/vi/e0RuGO_gGvA/0.jpg)](https://www.youtube.com/watch?v=e0RuGO_gGvA)

#

### **1. What You Need** :gift:
To build this project, you need the following items:
- 1 Arduino Uno board
- 5 LED matrix 8x8 (you can use up to 8 pcs)
- 5 IC driver MAX7219 (for each LED matrix)
- 1 Bluetooth HC-05 module
- some jumper wires
- Arduino IDE ([download here](https://www.arduino.cc/en/Main/Software))
- Lin Bluetooth SPP Android App (I've attached its __.apk__ in this repo)
- Arduino LedControl library (attached)

#

### **2. Schematics** :wrench::hammer:

Gather your parts then follow the schematics below.
 - **DIN** (Data In) from first MAX7219 to Arduino **pin 12**
 - **CLK** (Clock) from first MAX7219 to Arduino **pin 11**
 - **CS** (Chip Select) from first MAX7219 to Arduino **pin 10**
 - **GND** from first MAX7219 to Arduino **GND**
 - **VCC** from first MAX7219 to Arduino **5V**
 - Between MAX7219: DIN(in) to DIN(out), CS(in) to CS(out), CLK(in) to CLK(out), GND(in) to GND(out), VCC(in) to VCC(out)
 - **RX** Bluetooth HC-05 to **TX** Arduino
 - **TX** Bluetooth HC-05 to **RX** Arduino
 - **GND** Bluetooth HC-05 to Arduino **GND**
 - **VCC** Bluetooth HC-05 to Arduino **5V**

![Bluetooth Running Text 1.0 schematics](https://raw.githubusercontent.com/LintangWisesa/Bluetooth-Running-Text-1.0/master/BluetoothRunningText1.png)

#

### **3. Sketch** :clipboard:

- You need to remove the RX and TX wires from Arduino, before uploading the sketch to your Arduino board. 
- Extract _**LedControl**_ library then copy it to _C:\\...\Documents\Arduino\libraries_.
- Open Arduino IDE then copy sketch below. Make sure you have chosen the right option for **_Board_** and **_Port_** under **_Tools_** menu, then upload to your Arduino board.

```c++
#include "LedControl.h"

int jumlahMatrix = 5;
// Total amount of LED Matrix, up to 8 pcs!

int speedDelay = 12;
int panjangKarakter = 25;
char incomingByte;
char Buffer[200];
char Buffer_inverse[200];
char frase[50];
LedControl lc = LedControl(12, 11, 10, jumlahMatrix); 

int karakterAscii[480] = {
    0x00,0x00,0x00,0x00,0x00, /* spasi*/
    0x00,0xF6,0xF6,0x00,0x00, /* ! tandaSeru */
    0x00,0xE0,0x00,0xE0,0x00, /* " kutipDua */
    0x28,0xFE,0x28,0xFE,0x28, /* # tagar */
    0x00,0x64,0xD6,0x54,0x08, /* $ dollar */
    0xC2,0xCC,0x10,0x26,0xC6, /* % persen */
    0x4C,0xB2,0x92,0x6C,0x0A, /* & dan */
    0x00,0x00,0xE0,0x00,0x00, /* ' apostrof */
    0x00,0x38,0x44,0x82,0x00, /* ( kurungBuka */
    0x00,0x82,0x44,0x38,0x00, /* ) kurungTutup */
    0x88,0x50,0xF8,0x50,0x88, /* * asterisk */
    0x08,0x08,0x3E,0x08,0x08, /* + plus */
    0x00,0x00,0x05,0x06,0x00, /* , koma */
    0x08,0x08,0x08,0x08,0x08, /* - minus */
    0x00,0x00,0x06,0x06,0x00, /* . titik */
    0x02,0x0C,0x10,0x60,0x80, /* / slash */
    0x7C,0x8A,0x92,0xA2,0x7C, /* 0 */
    0x00,0x42,0xFE,0x02,0x00, /* 1 */
    0x42,0x86,0x8A,0x92,0x62, /* 2 */
    0x44,0x82,0x92,0x92,0x6C, /* 3 */
    0x10,0x30,0x50,0xFE,0x10, /* 4 */
    0xE4,0xA2,0xA2,0xA2,0x9C, /* 5 */
    0x3C,0x52,0x92,0x92,0x0C, /* 6 */
    0x80,0x86,0x98,0xE0,0x80, /* 7 */
    0x6C,0x92,0x92,0x92,0x6C, /* 8 */
    0x60,0x92,0x92,0x94,0x78, /* 9 */
    0x00,0x00,0x36,0x36,0x00, /* : titikDua */
    0x00,0x00,0x35,0x36,0x00, /* ; titikKoma */
    0x10,0x28,0x44,0x82,0x00, /* < kurangDari */
    0x28,0x28,0x28,0x28,0x28, /* = samaDengan */
    0x00,0x82,0x44,0x28,0x10, /* > lebihDari */
    0x40,0x80,0x8A,0x90,0x60, /* ? tandaTanya */
    0x7C,0x82,0xBA,0xBA,0x62, /* @ at */
    0x3E,0x48,0x88,0x48,0x3E, /* A */
    0xFE,0x92,0x92,0x92,0x6C, /* B */
    0x7C,0x82,0x82,0x82,0x44, /* C */
    0xFE,0x82,0x82,0x82,0x7C, /* D */
    0xFE,0x92,0x92,0x92,0x82, /* E */
    0xFE,0x90,0x90,0x90,0x80, /* F */
    0x7C,0x82,0x82,0x8A,0x4E, /* G */
    0xFE,0x10,0x10,0x10,0xFE, /* H */
    0x82,0x82,0xFE,0x82,0x82, /* I */
    0x84,0x82,0xFC,0x80,0x80, /* J */
    0xFE,0x10,0x28,0x44,0x82, /* K */
    0xFE,0x02,0x02,0x02,0x02, /* L */
    0xFE,0x40,0x20,0x40,0xFE, /* M */
    0xFE,0x60,0x10,0x0C,0xFE, /* N */
    0x7C,0x82,0x82,0x82,0x7C, /* O */
    0xFE,0x90,0x90,0x90,0x60, /* P */
    0x7C,0x82,0x82,0x86,0x7E, /* Q */
    0xFE,0x90,0x98,0x94,0x62, /* R */
    0x64,0x92,0x92,0x92,0x4C, /* S */
    0x80,0x80,0xFE,0x80,0x80, /* T */
    0xFC,0x02,0x02,0x02,0xFC, /* U */
    0xF8,0x04,0x02,0x04,0xF8, /* V */
    0xFC,0x02,0x0C,0x02,0xFC, /* W */
    0xC6,0x28,0x10,0x28,0xC6, /* X */
    0xC0,0x20,0x1E,0x20,0xC0, /* Y */
    0x86,0x8A,0x92,0xA2,0xC2, /* Z */
    0x00,0x00,0xFE,0x82,0x00, /* [ */
    0x00,0x00,0x00,0x00,0x00, /* asterisk */
    0x80,0x60,0x10,0x0C,0x02, /* ] */
    0x20,0x40,0x80,0x40,0x20, /* ^ */
    0x01,0x01,0x01,0x01,0x01, /* _ */
    0x80,0x40,0x20,0x00,0x00, /* ` */
    0x04,0x2A,0x2A,0x2A,0x1E, /* a */
    0xFE,0x12,0x22,0x22,0x1C, /* b */
    0x1C,0x22,0x22,0x22,0x14, /* c */
    0x1C,0x22,0x22,0x12,0xFE, /* d */
    0x1C,0x2A,0x2A,0x2A,0x18, /* e */
    0x10,0x7E,0x90,0x80,0x40, /* f */
    0x18,0x25,0x25,0x25,0x1E, /* g */
    0xFE,0x10,0x10,0x10,0x0E, /* h */
    0x00,0x12,0x5E,0x02,0x00, /* i */
    0x02,0x01,0x01,0x11,0x5E, /* j */
    0xFE,0x08,0x08,0x14,0x22, /* k */
    0x00,0x82,0xFE,0x02,0x00, /* l */
    0x3E,0x20,0x1C,0x20,0x1E, /* m */
    0x3E,0x20,0x20,0x20,0x1E, /* n */
    0x1C,0x22,0x22,0x22,0x1C, /* o */
    0x3F,0x24,0x24,0x24,0x18, /* p */
    0x18,0x24,0x24,0x3F,0x01, /* q */
    0x3E,0x10,0x20,0x20,0x10, /* r */
    0x12,0x2A,0x2A,0x2A,0x04, /* s */
    0x00,0x10,0x3C,0x12,0x04, /* t */
    0x3C,0x02,0x02,0x02,0x3E, /* u */
    0x30,0x0C,0x02,0x0C,0x30, /* v */
    0x38,0x06,0x18,0x06,0x38, /* w */
    0x22,0x14,0x08,0x14,0x22, /* x */
    0x38,0x05,0x05,0x05,0x3E, /* y */
    0x22,0x26,0x2A,0x32,0x22, /* z */
    0x00,0x10,0x6C,0x82,0x82, /* { */
    0x00,0x00,0xFF,0x00,0x00, /* | */
    0x04,0x02,0xFF,0x02,0x04, /* |, arrow */
    0x82,0x82,0x6C,0x10,0x00, /* } */
    0x08,0x10,0x18,0x08,0x10  /* ~ */
};

void setup() {
    for(int i = 0; i < jumlahMatrix; i++) {
        lc.shutdown(i, false);
        lc.setIntensity(i, 4);
        lc.clearDisplay(i);
    }

    BufferBuilding("      simplinnovation", 21);
    Serial.begin(9600);
}

void loop() { 
    DrawText(panjangKarakter);
    if (Serial.available() > 0) {
        panjangKarakter = Serial.available();

        for  (int i = 0; i < panjangKarakter; i++) {
            incomingByte = Serial.read();
            frase[i] = incomingByte;
        }

        for(int k = 0; k < 200; k++) {
            Buffer[k]=0;
            Buffer_inverse[k]=0;
        }

        BufferBuilding(frase, panjangKarakter);
    }
}

void BufferBuilding(char * tampilString, byte pjgString){
    int i =0;
    for(int k = 0; k < pjgString; k++){
        int simbol = k;
        Buffer[i+0] = karakterAscii[((tampilString[simbol] - 32) * 5) + 0];  
        Buffer[i+1] = karakterAscii[((tampilString[simbol] - 32) * 5) + 1];  
        Buffer[i+2] = karakterAscii[((tampilString[simbol] - 32) * 5) + 2];  
        Buffer[i+3] = karakterAscii[((tampilString[simbol] - 32) * 5) + 3];  
        Buffer[i+4] = karakterAscii[((tampilString[simbol] - 32) * 5) + 4];  
        Buffer[i+5] = B00000000;
        i=i+6;
    }
    for(int k = 0; k < pjgString*6; k++){
        for(int i = 0; i < 8; i++) {
            bitWrite(Buffer_inverse[k], i, bitRead(Buffer[k] ,7-i));
        }
    }
}

void DrawText( byte pjgString) {
    for(int k = 0; k < pjgString*6; k++) { 
        delay(speedDelay);
        for (int m = 0; m < jumlahMatrix; m++) {                     
            if (m % 2 ==  0){                
                for (int i = 0; i < 8; i++) {
                    lc.setRow(m, 7-i, Buffer[i+k+8*m]);
                }
            } else {
                for (int i = 0; i < 8; i++) {
                    lc.setRow(m, i, Buffer_inverse[i+k+8*m]);
                }
            }
        }
    }
}
```

#

### **4. Have Fun!** :joy:
- After uploading process is done, reconnect TX & RX from Bluetooth module to RX & TX on Arduino board then restart it.
- Open LIN Bluetooth SPP App, then connect to your Bluetooth module. If the Bluetooth module asks for a password, by default it's __'1234'__ (except you have changed it!).
- After all connected, you can type a message, send it, then it will be displayed on our running text automatically. Have fun! 

#

#### Lintang Wisesa :love_letter: _lintangwisesa@ymail.com_

[Facebook](https://www.facebook.com/lintangbagus) | 
[Twitter](https://twitter.com/Lintang_Wisesa) |
[Google+](https://plus.google.com/u/0/+LintangWisesa1) |
[Youtube](https://www.youtube.com/user/lintangbagus) | 
:octocat: [GitHub](https://github.com/LintangWisesa) |
[Hackster](https://www.hackster.io/lintangwisesa)

