#include <dht.h>
#include <LiquidCrystal.h>

#define BUZZER  2  
#define BUZZER2  A2    
#define BUZZER3  A4  
#define DHT_PIN  3
#define TRIG 6
#define ECHO 4
#define RS 12
#define E 11
#define LCD_PIN_DB4 7
#define LCD_PIN_DB5 8
#define LCD_PIN_DB6 9
#define LCD_PIN_DB7 10
#define LED 5
#define RED_LED A3
#define PUSHBUTTON  13
#define LDR_PIN A0
#define FLAME_PIN A1
#define RAIN_PIN A5

long t ;
int d;
dht DHT; // Creats a DHT object
LiquidCrystal lcd(RS,E,LCD_PIN_DB4,LCD_PIN_DB5,LCD_PIN_DB6,LCD_PIN_DB7);  // Creates an LCD object. Parameters: (rs, enable, d4, d5, d6, d7)

void setup() {
    Serial.begin(9600);
    lcd.begin(16, 2); // Initializes the interface to the LCD screen, and specifies the dimensions (width and height) of the display.
    pinMode(TRIG,OUTPUT);
    pinMode(ECHO,INPUT);
    pinMode(LED,OUTPUT);
    pinMode(BUZZER,OUTPUT);
    pinMode(PUSHBUTTON,INPUT);
    pinMode(LDR_PIN,INPUT);
    pinMode(RED_LED,OUTPUT);
    pinMode(FLAME_PIN,INPUT);
    pinMode(BUZZER2,OUTPUT);
    pinMode(BUZZER3,OUTPUT);
    pinMode(RAIN_PIN,INPUT);
}

void loop() {
  //DHT with LCD
  int readData = DHT.read(DHT_PIN);// Reads the data from the sensor
  float t = DHT.temperature;
  float h = DHT.humidity;
  lcd.setCursor(0,0);   // Sets the location at which subsequent text written to the LCD will be displayed
  lcd.print("Temp.: "); // Prints string "Temp." on the LCD
  lcd.print(t); // Prints the temperature value from the sensor
  lcd.print(" C");
  lcd.setCursor(0,1);
  lcd.print("Humi.: ");
  lcd.print(h);
  lcd.print(" %");
  delay(2000);

  //ULTRASONIC with BUZZER
  digitalWrite(TRIG,LOW); // Clears the trigPin.
  delayMicroseconds(2);  
  digitalWrite(TRIG,HIGH);// Sets the trigPin on HIGH state for 10 micro seconds
  delayMicroseconds(10);
  digitalWrite(TRIG,LOW); 
  t = pulseIn(ECHO,HIGH);// Reads the echoPin, returns the sound wave travel time in microseconds
  d = (.034*t)/2;
  delay(1000);
  if(d < 100)
  {
    tone(BUZZER,100);
    delay(1000); //stops program for 1 second
  }
  else
  {
    noTone(BUZZER);
  }
 
  //LDR with LED
  int ldrStatus = analogRead(LDR_PIN); //read the status of the ldr value.
  if(ldrStatus <= 800)
  {
    digitalWrite(LED,HIGH); //It's dark and the led is on.
  }
  else
  {
    digitalWrite(LED,LOW); //It's light and the led is off.
  }

  //Fire sensor with red led and buzzer
  int flameValue = analogRead(FLAME_PIN);
  if(flameValue <= 800)
  {
    digitalWrite(RED_LED,HIGH); 
    tone(BUZZER2,100);
    delay(1000); 
  }
  else
  {
    digitalWrite(RED_LED,LOW);
    noTone(BUZZER);
  }

  //Rain sensor with buzzer
  int rainValue = analogRead(RAIN_PIN);
  if(rainValue <= 800)
  { 
    tone(BUZZER3,100);
    delay(1000); 
  }
  else
  {
    noTone(BUZZER);
  }

}
