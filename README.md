//TSC3200-LIGHT-SENSOR
// authur: Michael J Calisi
// created: 21/06/2018
#define s2 7 // control pin 
#define s3 8 // control pin 
#define s0 5 // Freq control
#define s1 6 // Freq control 
#define LED 4 // Illumination LED for sensor 
#define OUT 9 // Frequency output pin
#define clr 967  // white light polling frequency 
#define RLED 10  // red pin 
#define BLED 3  // blue pin
#define GLED 2 // green pin 
void prntData(unsigned int r,unsigned int b ,unsigned int g);
int  flagSet (unsigned int f);// sets flag bit if freq=> polling frequency
void clrMap ( int R ,int B ,int G);//maps frequency data to correct color 
void colorLED(int r_VAL,int b_VAL,int g_VAL);


 unsigned  int  bFreq ;
 unsigned  int  rFreq ;
 unsigned  int  gFreq ;
 const int  freq = 0  ;   // input frequency for clr 
void setup()
{
  Serial.begin(9600);
  pinMode (s0,OUTPUT); //Binary frequency scaling pins 
  pinMode (s1,OUTPUT);
  pinMode (s2,OUTPUT);//Binary Photo diode selector pins 
  pinMode (s3,OUTPUT);
  pinMode (LED,OUTPUT);
  pinMode (OUT,INPUT);
  pinMode (s2,OUTPUT);//Binary Photo diode selector pins 
  pinMode (s3,OUTPUT);
  digitalWrite(LED,HIGH);
  digitalWrite(s0,LOW);// Sets phot diode scaling to 20% (L,L,off), (L,H,2%) (H,L,20%) (H,H,100%)
  digitalWrite(s1,HIGH);
  pinMode(RLED,OUTPUT);// sets RGB pins as outputs
  pinMode(BLED,OUTPUT);
  pinMode(GLED,OUTPUT);
  
}

void loop() 
{ 
 switch ( flagSet(freq))// flag controlls switch statment
      {
  case 0 : Serial.print("no color \n");
           colorLED(freq,freq,freq);
           delay(400);
  break;

     case 1 :

       digitalWrite(s2,LOW);// sets photo diode type to Red
       digitalWrite(s3,LOW);
       rFreq =50000/pulseIn(OUT,LOW);
  
       digitalWrite(s2,LOW);// sets photo diode type to Blue
       digitalWrite(s3,HIGH); 
       bFreq =50000/ pulseIn(OUT,LOW); 
   
       digitalWrite(s2,HIGH);// sets photo diode type to Green 
       digitalWrite(s3,HIGH);
       gFreq =50000/ pulseIn(OUT,LOW);
  
         prntData(rFreq,bFreq ,gFreq);

      delay(2);
    break;
   
}
}
 
   
  void prntData (unsigned int r, unsigned int b ,unsigned int g)
  { 
  
   Serial.print("Red f=");
   Serial.print(r);
   Serial.print(" ");
   Serial.print("Blue f=");
   Serial.print(b);
   Serial.print(" ");
   Serial.print("Green f=");
   Serial.print(g);
   Serial.print(" ");
   clrMap(r,b,g);
   Serial.print("\n");
    delay(500); 
  
  }

int flagSet (unsigned int f)
{ 
  int flag =0;    
  digitalWrite(s2,HIGH);// sets photo diode type to Clear
    digitalWrite(s3,LOW);
      f =50000/pulseIn(OUT,LOW);

      if (f >= clr ) // sets flag bit if frequency >=CLR 
        {
      flag =1 ;  
          } 
     else 
         flag =0;
    
     return flag;  

}


void clrMap(int R,int B, int G)
{ int j;
  float r = R; 
  float b = B ;
  float g = G;
               r= r/236;// calculations to decrease output frequecny integers to n<10;
               b= b/173;
               g= g/149;
               
 j = (r+b+g)/11.3; 

 /*Serial.print(r);
 Serial.print(" ");
 Serial.print(b);
 Serial.print(" ");
 Serial.print(g);
 Serial.print(" ");
 Serial.print( j);
 */
      
 switch(j)
 {

case 0:

    if ((b > 1.3) && (b > r)&&(b>g))
    {
       Serial.print ("Color is Blue");
        colorLED( r=0, b, g=0);

    }

    if((g>1.0)&&(g>=b)&&(b>r))
       {
    
      Serial.print("Color is Green");
       colorLED( r=0, b=0, g);
   
         
       }

    if ( (r>1.1) && (b>g)&&(r>g) )
       {
      Serial.print("Color is Red");
      colorLED( r, b=0, g=0);
           
       }
    break;

case 1:
      Serial.print("Color is yellow");
        colorLED( r=3.57, b=0, g);
     
    break;

 }

}

void colorLED(int r_VAL,int b_VAL,int g_VAL)

{
   r_VAL=map(r_VAL,0.64,3.57,0,255);
   b_VAL=map(b_VAL,0.64,3.57,0,255);
   g_VAL=map(g_VAL,0.64,3.57,0,255);
   analogWrite(RLED,r_VAL);
   analogWrite(BLED,b_VAL);
   analogWrite(GLED,g_VAL);


   
  
}
