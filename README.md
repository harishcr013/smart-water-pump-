# smart-water-pump-
/* 
 *Smart Water Tank Pump Switcher
 *An Arduino-Based Project
 *Designed by T.K.Hareendran
 *Tested @ TechNode PROTOLABZ
 *IDE:Arduino 0022 / Board:Arduino UNO-R3 / Chip: ATmega328P-PU
 *Date:November 14,2014
 */

#include <EEPROM.h>

#define inputLevel1  4  //Tank Level-Low

#define inputLevel2  7  //Tank Level-High
#define modeSwitch   2 //Auto/Manual Mode Switch
// OUTPUT PINS

#define statLED  8 //System Status Indicator

#define modeLED  12  //Auto/Manual Mode Indicator
#define driveOutput 13 //Water Pump Swicth Output
#define memposL1  0  //Set constant eeprom position 0 to save Level1

int state=0;
int level = 0;
int lastlevel = 0;

void setup (){
  pinMode(modeSwitch,INPUT);   
  pinMode(inputLevel1,INPUT);          
  pinMode(inputLevel2,INPUT);            

  digitalWrite(modeSwitch, HIGH);     
  digitalWrite(inputLevel1, HIGH);    

  digitalWrite(inputLevel2, HIGH);     


  pinMode(driveOutput, OUTPUT);
  pinMode(statLED, OUTPUT);
  pinMode(modeLED, OUTPUT);

  level = EEPROM.read(memposL1); 
  lastlevel = level;           
  if ((level == 0)||(level>2)) { 
    level=1;			 
    refreshmem(); 
  }
  visualdrive(); 
  delay(1000);
  pumpdrive();
}

void visualdrive() {

  digitalWrite(statLED, LOW );

  if (level >1)   digitalWrite(statLED, HIGH );

}

void  pumpdrive(){ 
  if (level < 2)   digitalWrite(driveOutput, HIGH );
  if ((level == 2) & (state == 0)) {
    digitalWrite(driveOutput, LOW );
  }
  else {
    digitalWrite(driveOutput, HIGH );
  }
}

void refreshmem() {

  lastlevel = level;      
  EEPROM.write(memposL1, level);   
}

void loop () {
  if ( (digitalRead(modeSwitch) == LOW) & (state == 0) ) { 
    state=1;  
    digitalWrite(modeLED, state );        
    pumpdrive();
    delay(400);
  }

  if ( (digitalRead(modeSwitch) == LOW) & (state == 1)  ) { 
    state=0;  
    digitalWrite(modeLED, state );       
    pumpdrive();
    delay(400);
  } 


  if ( digitalRead(inputLevel1) == LOW  ) { 
    level = 1;
  }

  if ( digitalRead(inputLevel2) == LOW  ) { 
    level = 2;
  }

  if (level != lastlevel) {
    visualdrive();
    pumpdrive();
    refreshmem();
  }   

}
