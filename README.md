# traffic-lights
This project is the simulation of traffic lights using an Arduino with some circuitry. This was a project for my Real Time Embedded Systems module. As this was the first time I got to work with both hardware and software together this was very interesting to me and was very passionate in creating this project.
I learned how to code an Arduino in-junction with hardware components to create awesome things. Tinkering with different projects and seeing hardware components change in real-time because of software changes was fascinating to me.

The project at hand simulates traffic lights like that at a road construction site. It involves the use of 3 sets of LEDs to accurately represent the traffic lights, a daylight sensor to determine how long the traffic lights wait before changing, and a 'traffic button' that when pressed simulates a traffic sensor on a road, changing the traffic lights if the other side does not have traffic.
I developed this step by step as I found this the best approach to tackling the problem. First I setup the traffic lights without the daylight sensor or traffic sensors. On the software side this was just timing which lights turn on at which time.
After this was working I added the traffic sensors. I did this by delaying the lights for a minimum of two seconds and then running a for loop that iterated every second for a maximum of 10 seconds. At each iteration, it checks to see if there is traffic at the red light. If there is no traffic at the green light but some at the red light then the lights will begin to change.
The next step was adding the daylight sensor, this was done by reading the analog value of the sensor and checking it against a threshold of 150 that I arbitrarily set. This was then checked before each 'stage' of the traffic lights and the length of the lights are changed depending on if it is daytime or not.

### Full images of the project:


![image](https://user-images.githubusercontent.com/38580052/130058662-6a3ee3fd-1021-45b7-80ec-142455944474.png)
![image](https://user-images.githubusercontent.com/38580052/130058690-06325651-fca3-4085-9cb7-cefc72271c7e.png)
![image](https://user-images.githubusercontent.com/38580052/130058703-18da500e-6410-4a06-8268-2aeffe2acaec.png)

### here is a full look at the code:
```C
const int GreenLed_A = 3;
const int YellowLed_A = 4;
const int RedLed_A = 5;
const int Button_A = 2;

const int GreenLed_B = 10;
const int YellowLed_B = 11;
const int RedLed_B = 12;
const int Button_B = 9;

const int lightSensorPin = A0;
int analogValue = 0;
void setup() {
  // put your setup code here, to run once:

pinMode(GreenLed_A, OUTPUT);
pinMode(YellowLed_A, OUTPUT);
pinMode(RedLed_A, OUTPUT);
pinMode(Button_A, INPUT);
pinMode(GreenLed_B, OUTPUT);
pinMode(YellowLed_B, OUTPUT);
pinMode(RedLed_B, OUTPUT);
pinMode(Button_B, INPUT);
Serial.begin(9600);
}

void loop() {
  // put your main code here, to run repeatedly:
int delayInterval = 1000;
int switchStateB = LOW;
int switchStateA = LOW;
//Turn all lights off, resetting both sets of lights
reset();

//Read the initial state to see if there is traffic currently
switchStateB = digitalRead(Button_B);
switchStateA = digitalRead(Button_A);

//Execute light stage one
lightStageOne();
//Wait the minimum two seconds
delay(delayInterval*2);
//Check the light sensor for day or night time
analogValue = analogRead(lightSensorPin);

//If it's day time, follow day time plan
if(isDayTime(analogValue)){
  Serial.println("Day");
  //Set iteration variable to 8 because it is day time
  int limit = 8;
  //Call function to check for traffic
  GreenLight_A(switchStateA,switchStateB,delayInterval,limit);
}

//Otherwise it's night time, follow night time plan
else{
  Serial.println("Night");
   //Set iteration variable to 18 because it is night time
   int limit = 18; 
   //Call function to check for traffic   
   GreenLight_A(switchStateA,switchStateB,delayInterval,limit);
}

lightStageTwo();
delay(delayInterval*2);

lightStageThree();
delay(delayInterval*2);

lightStageFour();
//Wait the minimum two seconds
delay(delayInterval*2);

//Read the initial state to see if there is traffic currently
    switchStateB = digitalRead(Button_B);
    switchStateA = digitalRead(Button_A);

analogValue = analogRead(lightSensorPin);
if(isDayTime(analogValue)){
  Serial.println("Day");
    int limit = 8;
    GreenLight_B(switchStateA,switchStateB,delayInterval,limit);
}
else{
  Serial.println("Night");
    int limit = 18;
    GreenLight_B(switchStateA,switchStateB,delayInterval,limit);
}

lightStageFive();
delay(delayInterval*2);

lightStageSix();
delay(delayInterval*2);

}

void reset(){
digitalWrite(GreenLed_B, LOW);
digitalWrite(RedLed_A, LOW);
digitalWrite(YellowLed_A, LOW);
digitalWrite(YellowLed_B, LOW);
digitalWrite(GreenLed_A, LOW);
digitalWrite(RedLed_B, LOW);
}

void lightStageOne(){
digitalWrite(GreenLed_A, HIGH);
digitalWrite(RedLed_B, HIGH);
}

void lightStageTwo(){
digitalWrite(YellowLed_A, HIGH);
digitalWrite(GreenLed_A, LOW);
}

void lightStageThree(){
digitalWrite(RedLed_A, HIGH);
digitalWrite(GreenLed_A, LOW);
digitalWrite(YellowLed_A, LOW);
}

void lightStageFour(){
digitalWrite(GreenLed_B, HIGH);
digitalWrite(RedLed_B, LOW);
}

void lightStageFive(){
digitalWrite(GreenLed_B, LOW);
digitalWrite(YellowLed_B, HIGH);
}

void lightStageSix(){
digitalWrite(RedLed_B , HIGH);
digitalWrite(YellowLed_B, LOW);
}

bool isDayTime(int analogValue){
  if(analogValue <= 150){
    return false;
  }
  else{
    return true;
  }
}

void GreenLight_A(int switchStateA, int switchStateB, int delayInterval, int limit){
    for(int i = 0; i < limit; i++){
      //Read switch value at beginning of each iteration
      //To check for traffic 
      switchStateB = digitalRead(Button_B);
      switchStateA = digitalRead(Button_A);
      if(switchStateA == HIGH && switchStateB == HIGH){
        Serial.println("Traffic at b, but a has traffic so not changing");
      }
      else if(switchStateB == HIGH && switchStateA == LOW){
        Serial.println("There is traffic at B but none at A, changing lights");
        break;
      }
      delay(delayInterval);
    }
}

void GreenLight_B(int switchStateA, int switchStateB, int delayInterval, int limit){
    for(int i = 0; i < limit; i++){
      //Read switch value at beginning of each iteration
      //To check for traffic 
      switchStateB = digitalRead(Button_B);
      switchStateA = digitalRead(Button_A);
      if(switchStateA == HIGH && switchStateB == HIGH){
        Serial.println("Traffic at A, but B has traffic so not changing");
      }
      else if(switchStateB == LOW && switchStateA == HIGH){
        Serial.println("There is traffic at A but none at B, changing lights");
        break;
      }
      delay(delayInterval);
    }
}```
