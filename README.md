# Arduino-Snake-Game
Playing snake on a arduino

# What you need:

_-Arduino Uno
_-Joystick
_-8x8 MAX7219 DotMatrix-Module
_-Breadbord (optional)

# How to connect:

## Joystick:

  GND - GND
  
  +5v - 5v
  
  JRx - A0
  
  JRy - A1
  
  SW  - Pin9
  
## Matrix-Module

  VCC - 5v
  
  GND - GND
  
  DIN - Pin12
  
  cs  - Pin10
  
  CLK - Pin11
  
## Pictures
![Bild2](https://user-images.githubusercontent.com/72698237/128373302-599aa7ca-2eff-47ba-888a-36b2b5bc5e66.PNG)

![arduino-led-matrix-max7219](https://user-images.githubusercontent.com/72698237/128373305-c8d42ae3-a503-4c62-bf4c-6ee8ff975581.jpg)
![SchaltplanSnake](https://user-images.githubusercontent.com/72698237/128373304-121ad1b0-9c08-4ae0-b753-f5c60c180e91.PNG)

# Code

```
#include "LedControl.h"
#define joyX A0   //Joystick x richtung auf A0
#define joyY A1   //Joystick y richtung auf A1


int movewt = 500; //speed (lower = faster , higher = slower) milliseconds to move
int lengthtowin = 20; //determine the number of apples you need to win

int joybut = 9; //joystick button pin 9

int maxlength = 3; //length of the snake

int yValue;
int xValue;
int Punkte;
char direction;
int row = 4;
int col = 4;
bool neustart = true;
LedControl lc=LedControl(12,11,10,1);
bool dead = false;
int matrix [8][8]; 

int apfelPosition [8][8];
bool needapple = true;
int ax; //apple X position
int ay; //apple Y position
int atime = 200; //apple blink speed (lower = faster , higher = slower)1
bool leuchtet = false;
unsigned long lastappletime;

bool won = false;
unsigned long lasttime;
byte lachen[8]={B11111111, B11000011, B10101001, B10000101, B10000101, B10101001, B11000011, B11111111};
byte traurig[8]={B11111111, B11000011, B10100101, B10001001, B10001001, B10100101, B11000011, B11111111};
void setup() {
  delay(2000);
  pinMode(joybut, INPUT_PULLUP);
  // put your setup code here, to run once:
  Serial.begin (9600);
  Serial.println("Guten Tag");
  lc.shutdown(0,false);
  lc.setIntensity(0,8);
  lc.clearDisplay(0);
  lc.setLed(0,row,col,true);
  matrix [row][col] = 1;
}

void loop() {
  if (won == false){
  if (dead == false){
  xValue = analogRead(joyX);
  yValue = analogRead(joyY);  
  richtung();
  apfeltocollect();
  weiterfahren();
  }
  
  if (dead == true)
  {
    col = 4;
    row = 4;
    for(int x = 0; x <8 ; x ++){    
  	    for(int y = 0 ; y <8 ; y++){
          
            matrix[x][y] = 0;
            
          
        }
    }
    for(int x = 0; x <8 ; x ++){    
  	    for(int y = 0 ; y <8 ; y++){
          
            apfelPosition[x][y] = 0;
            
          
        }
    }
    maxlength = 3;
    for (int i=0; i<8; i++){
     lc.setRow(0,i,traurig[i]);
    }
    if(digitalRead(joybut)== 0){
        
        dead = false;
        lc.clearDisplay(0);
        lc.setLed(0,row,col,true);
        matrix [row][col] = 1;
      }
  }
  if (lengthtowin<=maxlength){
    won = true;
  }
  }
  if (won == true)
  {
    neustart = true;
    col = 4;
    row = 4;
    for(int x = 0; x <8 ; x ++){    
  	    for(int y = 0 ; y <8 ; y++){
          
            matrix[x][y] = 0;
            
          
        }
    }
    for(int x = 0; x <8 ; x ++){    
  	    for(int y = 0 ; y <8 ; y++){
          
            apfelPosition[x][y] = 0;
            
          
        }
    }
    maxlength = 3;
    for (int i=0; i<8; i++){
     lc.setRow(0,i,lachen[i]);
    }
    if(digitalRead(joybut)== 0){
        
        won = false;
        lc.clearDisplay(0);
        lc.setLed(0,row,col,true);
        matrix [row][col] = 1;
      }
  // put your main code here, to run repeatedly:

}}
void apfeltocollect(){
  if (needapple == true){ //generate new apple
    needapple = false;
    ay = random(0 , 7);
    ax = random(0 , 7);
    while(matrix[ax][ay] != 0){
      ay = random(0 , 7);
      ax = random(0 , 7);
    }
    apfelPosition[ax][ay] = 1;
  }
  if (ax == row && ay == col && needapple == false){ // collect apple
    needapple = true;
    for(int x = 0; x <8 ; x ++){    
  	    for(int y = 0 ; y <8 ; y++){
          
            apfelPosition[x][y] = 0;
            
          
        }
    }
    maxlength ++;

  }
  if (needapple == false){ // blinken
    if(leuchtet == false && millis()-lastappletime > atime){
      lc.setLed(0,ax,ay,true);
      lastappletime = millis();
      leuchtet = true;
      }
    else if(leuchtet == true && millis()-lastappletime > atime){
      lc.setLed(0,ax,ay,false);
      lastappletime = millis();
      leuchtet = false;
    }
  }


}
void weiterfahren(){
  
  if (millis()- lasttime > movewt){
    lasttime = millis();
  switch (direction){
    case 'N':
      col --;
      Serial.println("Nord");
      break;
    case 'O':

      Serial.println("Ost");
      row --;
      break;
    case 'S':
      Serial.println("South");
       col ++;
      break;
     
    
    case 'W':
      Serial.println("West");
      row ++;
      break;
    default:
      Serial.println("MoveJoystick");
      break;
  
  }
  if (direction == 'W' ||direction == 'O' ||direction == 'S' ||direction == 'N' ){
  if (row <0 || row >7 || col < 0 || col > 7) //ob es in den Rand gefahren ist
  {
    Serial.println("DED because of wall");
    neustart = true;
    dead = true;

  }
  else if(matrix [row][col] > 0 && matrix [row][col] != maxlength ) //if touches snake //(
   {
    Serial.println("DED because of snake");
    neustart = true;
    dead = true;
    
  }
  else{
      for(int x = 0; x <8 ; x ++){    //remove last part of snake
  	    for(int y = 0 ; y <8 ; y++){
          if (matrix[x][y] == maxlength){
            matrix[x][y] = 0;
            lc.setLed(0,x,y,false);
          }
        }

      }

     // move numbers back
     for(int l = maxlength - 1; l > 0 ; l --){
       for(int x = 0; x <8 ; x ++){    
  	    for(int y = 0 ; y <8 ; y++){
          if (matrix[x][y] == l){
            matrix[x][y] = l + 1;
            
          }
        }
      
     }
     //set new point
     matrix [row][col] = 1;
     lc.setLed(0,row,col,true);

    }
    }
   }
  }
  }
void richtung(){
  if (xValue > 700){
    if(direction != 'N'){
      direction = 'S';
  }}

  else if(xValue < 250){
    if(direction != 'S'){
      direction = 'N';
    }
  }
  
  else if (yValue > 620){
    if (direction != 'O'){
    direction = 'W';
    }
  }
  else if (yValue < 400)
  {
    if (direction != 'W'){
    direction = 'O';
  }}
  else if(neustart == true){
    neustart = false;
    direction= 'p';
  }
 // Serial.println(direction);
}


```


# How to play

Move the joystick into any direction. Try to collect blinking point (apple) and you get bigger. Do not touch yourself or the border of the Led matrix, otherwise you die. If you died you can restart by pressing the Joystick button.

# Please Subscribe to my Youtube Channel

 ## https://www.youtube.com/channel/UCUKGpRmZLFh_AmThMi59b7w
 
# Snake Pictures
<img src="https://user-images.githubusercontent.com/72698237/128375020-615409d0-ef4f-4ee4-a9f0-65fd1edc4947.JPG" width="600">
<img src="https://user-images.githubusercontent.com/72698237/128374993-de001742-5644-4fe0-ad0a-cdfb85e244e4.JPG" width="600">
<img src="https://user-images.githubusercontent.com/72698237/128375047-e0a48d7c-5be8-44c6-b8e2-343f6127a284.JPG" width="600">1
<img src="https://user-images.githubusercontent.com/72698237/128375065-6fd96ed7-c541-420a-87e8-33be77a06dc6.JPG" width="600">
