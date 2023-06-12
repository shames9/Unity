# Misshapen Space
Collaborative project with Danqi Wang

## Original work link
As the Unity files are too large to upload to GitHub, we use Google Drive instead, please click on the link below to visit the original work.
https://drive.google.com/drive/folders/1goo6oy6cj3WwYuU3fMs6RjEmRKE3CdEC?usp=sharing

## Background
In a mysterious space station, scientists have conducted a ground-breaking experiment in memory. They developed a technology called the "Memory Fusion Device", which can fuse human memories directly into the physical environment. However, the experiment exploded and the device's energy stones were split into four parts and scattered in different spaces. Only by collecting the scattered pieces of energy stones by walking through the four spaces following the routes remembered in the fusion device, can people restore the space station to its normal state. Now you are the hero of this mission and only your memory and skill can save this mysterious space station.

## Design & Development
### Game flow
![Unity](https://github.com/shames9/Unity/blob/main/images/236e50f688f9aaae9549e74114f63a5.png)

### Interactive approach
- Using an Arduino to create a custom physics button, the player needs to press the button to control the main character's movement during the game. Once the game starts, the player must remember the route and press the button when needed to indicate the direction of the main character's movement. (Implemented)
- Use the Arduino to create a physical energy stone interactive device that lights up with LED lights under the energy stone pieces when the player passes the level. The player collects all the energy stone pieces to light up the physical energy stone device. (Not implemented yet)

## What I did
### Character controller
I have used ardunio and switch buttons to create a simple task controller.
...const int butPin1 = 6;
const int butPin2 = 7;
const int butPin3 = 4;
const int butPin4 = 5;
const int butPin5 = 10;

void setup() {
  // put your setup code here, to run once:
  Serial.begin(9600);

  pinMode(butPin1, INPUT);
  pinMode(butPin2, INPUT);
  pinMode(butPin3, INPUT);
  pinMode(butPin4, INPUT);
  pinMode(butPin5, INPUT);
  
  digitalWrite(butPin1, HIGH);
  digitalWrite(butPin2, HIGH);
  digitalWrite(butPin3, HIGH);
  digitalWrite(butPin4, HIGH);
  digitalWrite(butPin5, HIGH);
}

void loop() {
  // put your main code here, to run repeatedly:
 if(digitalRead(butPin1) == LOW){
   Serial.write("LEFT");
   Serial.write(1);
   Serial.flush();
   delay(20);
 }

 if(digitalRead(butPin2) == LOW){
   Serial.print("RIGHT");
   Serial.write(2);
   Serial.flush();
   delay(20);
 }

  if(digitalRead(butPin3) == LOW){
   Serial.write("FORWARD");
   Serial.write(3);
   Serial.flush();
   delay(20);
 }

 if(digitalRead(butPin4) == LOW){
   Serial.write("BACK");
   Serial.write(4);
   Serial.flush();
   delay(20);
 }

if (digitalRead(butPin5) == LOW) {
  Serial.write("JUMP");
  Serial.write(5);
  Serial.flush();
  delay(20);
}

}
...


### Unity-UI

### Unity-Sound

### Unity-Build, lighting, particle systems, animation

### Arduino-Energy stone devices
