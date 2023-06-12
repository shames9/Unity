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
```ruby
const int butPin1 = 6;
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
```
![Unity](https://github.com/shames9/Unity/blob/main/images/24de1e468ac924b0f7e9e7cc11f4abe.jpg)


### Demo&Developmet
I then started by creating a scene in unity along with characters and character animations. The unity code was written so that my unity could read the signals from my serial port COM3 to control the movement of the characters in unity.
```ruby
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using System.IO.Ports;
public class mama : MonoBehaviour

{
    // public GameObject gameOverCanvas;

    //  private void OnCollisionEnter(Collision collision)
    // {
    //     if (collision.gameObject.CompareTag("Player")) // 假设玩家对象具有"Player"标签
    //     {
    //         ShowGameOver();
    //     }
    // }
    SerialPort sp = new SerialPort("COM3", 9600); // 设置连接到计算机上的 Arduino 的端口
    private int receivedByte = 0; // 存储读取的字节
   // private Animator _animator;

    float horizontal;
    float vertical;
    // Start is called before the first frame update
    void Start()
    {
        sp.Open();
        sp.ReadTimeout = 1;

   //     _animator = GetComponent<Animator>();
    }

    // Update is called once per frame
    void Update()
    {
        //  if (Input.GetKeyDown(KeyCode.R))
        // {
        //     RestartGame();
        // }
    
    

        if (sp.IsOpen)
        {
            try
            {
                if (sp.BytesToRead > 0)
                {
                    receivedByte = sp.ReadByte();
                    Debug.Log("Received Byte: " + receivedByte); // 调试语句
                }
                else
                {
                    horizontal =0;
                    vertical =0;
                }

                if (receivedByte == 1)
                {
                    horizontal =1;
                    Debug.Log("按钮1");
                }
                else if (receivedByte == 2)
                {
                    horizontal =-1;
                    Debug.Log("按钮2");
                }
                else if (receivedByte == 3 || receivedByte == 5)
                {
                    vertical =1;
                    Debug.Log("按钮3");
                }
                else if (receivedByte == 4)
                {
                    vertical =-1;
                    Debug.Log("按钮4");
                }

                // 重置 receivedByte
                receivedByte = 0;
            }
            catch (System.Exception ex)
            {
                Debug.LogError("Error: " + ex.Message);
            }          
        }

        // float horizontal = Input.GetAxis("Horizontal");
        // float vertical = Input.GetAxis("Vertical");
        Vector3 dir = new Vector3(horizontal, 0, vertical);
        //float moveSpeed = 5f; // 设置移动速度，可以根据需求调整

        if (dir != Vector3.zero)
        {
            transform.rotation = Quaternion.LookRotation(dir);
         //   _animator.SetBool("isRun", true);
         //  Debug.Log(_animator.GetBool("isRun"));
             transform.Translate(Vector3.forward  * Time.deltaTime);
        }
        else
        {
          //  _animator.SetBool("isRun", false);
          //  Debug.Log(_animator.GetBool("isRun"));
        }

        if (Input.GetKeyDown(KeyCode.Space))
        {
         //   _animator.SetTrigger("Jump-up");
            StartCoroutine(TriggerJumpFloat());
        }
    }

    IEnumerator TriggerJumpFloat()
    {
        yield return new WaitForSeconds(0.5f); // 调整等待时间
       // _animator.SetTrigger("Jump-float");
        yield return new WaitForSeconds(0.5f); // 调整等待时间
       // _animator.SetTrigger("Jump-down");
    }

    // private void OnCollisionEnter(Collision collision)
    // {
    //     if (collision.gameObject.CompareTag("brick"))
    //     {
    //         Debug.Log("碰撞碰撞碰撞碰撞碰撞碰撞碰撞碰撞碰撞碰撞");
    //         // 在碰撞发生的位置生成砖块
    //         //Instantiate(brickPrefab, this.transform.position, Quaternion.identity);
    //     }
    // }
}
```
![Unity](https://github.com/shames9/Unity/blob/main/images/aa3e57b5eb74a83f63efe2e141f4395.png)

### Unity-Sound

### Unity-Build, lighting, particle systems, animation

### Arduino-Energy stone devices
