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

### Character movement and maze floor generation in unity
After I imported the scene built by the group, I found that my original control method was not compatible with the version scene. After I looked up information and watched instructional videos, I chose the Third Person Character controller to control the characters.

Keyboard input and camera follow
```ruby
using System;
using UnityEngine;
using UnityStandardAssets.CrossPlatformInput;

namespace UnityStandardAssets.Characters.ThirdPerson
{
    [RequireComponent(typeof (ThirdPersonCharacter))]
    public class ThirdPersonUserControl : MonoBehaviour
    {
        private ThirdPersonCharacter m_Character; // A reference to the ThirdPersonCharacter on the object
        private Transform m_Cam;                  // A reference to the main camera in the scenes transform
        private Vector3 m_CamForward;             // The current forward direction of the camera
        private Vector3 m_Move;
        private bool m_Jump;                      // the world-relative desired move direction, calculated from the camForward and user input.

        
        private void Start()
        {
            // get the transform of the main camera
            if (Camera.main != null)
            {
                m_Cam = Camera.main.transform;
            }
            else
            {
                Debug.LogWarning(
                    "Warning: no main camera found. Third person character needs a Camera tagged \"MainCamera\", for camera-relative controls.", gameObject);
                // we use self-relative controls in this case, which probably isn't what the user wants, but hey, we warned them!
            }

            // get the third person character ( this should never be null due to require component )
            m_Character = GetComponent<ThirdPersonCharacter>();
        }


        private void Update()
        {
            if (!m_Jump)
            {
                m_Jump = CrossPlatformInputManager.GetButtonDown("Jump");
            }
        }


        // Fixed update is called in sync with physics
        private void FixedUpdate()
        {
            // read inputs
            float h = CrossPlatformInputManager.GetAxis("Horizontal");
            float v = CrossPlatformInputManager.GetAxis("Vertical");
            bool crouch = Input.GetKey(KeyCode.C);

            // calculate move direction to pass to character
            if (m_Cam != null)
            {
                // calculate camera relative direction to move:
                m_CamForward = Vector3.Scale(m_Cam.forward, new Vector3(1, 0, 1)).normalized;
                m_Move = v*m_CamForward + h*m_Cam.right;
            }
            else
            {
                // we use world-relative directions in the case of no main camera
                m_Move = v*Vector3.forward + h*Vector3.right;
            }
#if !MOBILE_INPUT
            // walk speed multiplier
            if (Input.GetKey(KeyCode.LeftShift)) {

              
            }

            else
            {
                m_Move *= 0.5f;
            }
#endif

            // pass all parameters to the character control script
            m_Character.Move(m_Move, crouch, m_Jump);
            m_Jump = false;
        }
    }
}
```

### Integrating the ardunio control code into Third Person Control

```ruby
using UnityEngine;
using System.IO.Ports;


namespace UnityStandardAssets.Characters.ThirdPerson
{
	[RequireComponent(typeof(Rigidbody))]
	[RequireComponent(typeof(CapsuleCollider))]
	[RequireComponent(typeof(Animator))]
	public class ThirdPersonCharacter : MonoBehaviour
	{
		// Movement and rotation speed parameters
		[SerializeField] float m_MovingTurnSpeed = 360;
		[SerializeField] float m_StationaryTurnSpeed = 180;

		// Jumping parameters
		[SerializeField] float m_JumpPower = 12f;

		// Gravity and animation speed parameters
		[Range(1f, 4f)][SerializeField] float m_GravityMultiplier = 2f;
		[SerializeField] float m_RunCycleLegOffset = 0.2f; //specific to the character in sample assets, will need to be modified to work with others
		[SerializeField] float m_MoveSpeedMultiplier = 1f;
		[SerializeField] float m_AnimSpeedMultiplier = 1f;

		// Ground testing parameters
		[SerializeField] float m_GroundCheckDistance = 0.1f;

        // Component references
		Rigidbody m_Rigidbody;
		Animator m_Animator;

		// Move status parameter
		bool m_IsGrounded;
		float m_OrigGroundCheckDistance;
		const float k_Half = 0.5f;
		float m_TurnAmount;
		float m_ForwardAmount;
		Vector3 m_GroundNormal;
		float m_CapsuleHeight;
		Vector3 m_CapsuleCenter;
		CapsuleCollider m_Capsule;
		bool m_Crouching;

        // Arduino Serial communication parameters
		SerialPort sp = new SerialPort("COM3", 9600);
        private int receivedByte = 0;
		private float arduinoHorizontal;
        private float arduinoVertical;

		float horizontal;
        float vertical;


		void Start()
		{
			// Get component references
			m_Animator = GetComponent<Animator>();
			m_Rigidbody = GetComponent<Rigidbody>();
			m_Capsule = GetComponent<CapsuleCollider>();
			m_CapsuleHeight = m_Capsule.height;
			m_CapsuleCenter = m_Capsule.center;

			// Set rigid body constraints
			m_Rigidbody.constraints = RigidbodyConstraints.FreezeRotationX | RigidbodyConstraints.FreezeRotationY | RigidbodyConstraints.FreezeRotationZ;
			m_OrigGroundCheckDistance = m_GroundCheckDistance;
        
		    // Open the serial port
			sp.Open();
            sp.ReadTimeout = 1;
		}

		void Update()
    {
       
    
    
// Arduino part

       if (sp.IsOpen)
    {
        try
        {
            if (sp.BytesToRead > 0)
            {
                receivedByte = sp.ReadByte();
                Debug.Log("Received Byte: " + receivedByte); // Debugging statements
            }
            else
            {
                arduinoHorizontal = 0;
                arduinoVertical = 0;
            }

            // Set horizontal and vertical values according to the data sent by the Arduino
            if (receivedByte == 1)
            {
                arduinoHorizontal = -1;
                Debug.Log("Button1");
            }
            else if (receivedByte == 2)
            {
                arduinoHorizontal = 1;
                Debug.Log("Button2");
            }
            else if (receivedByte == 3 || receivedByte == 5)
            {
                arduinoVertical = -1;
                Debug.Log("Button3");
            }
            else if (receivedByte == 4)
            {
                arduinoVertical = 1;
                Debug.Log("Button4");
            }

            // reset receivedByte
            receivedByte = 0;
        }
        catch (System.Exception ex)
        {
            Debug.LogError("Error: " + ex.Message);
        }
    }

    // Apply the horizontal and vertical values of the Arduino input to the movement parameters of the character
    Vector3 moveDirection = new Vector3(arduinoHorizontal, 0, arduinoVertical);
    bool crouch = false; // Set whether to crouch or not as required
    bool jump = false; // Set to jump or not as required
    Move(moveDirection, crouch, jump);
	}



		public void Move(Vector3 move, bool crouch, bool jump)
		{

			// convert the world relative moveInput vector into a local-relative
			// turn amount and forward amount required to head in the desired
			// direction.
			if (move.magnitude > 1f) move.Normalize();
			move = transform.InverseTransformDirection(move);
			CheckGroundStatus();
			move = Vector3.ProjectOnPlane(move, m_GroundNormal);
			m_TurnAmount = Mathf.Atan2(move.x, move.z);
			m_ForwardAmount = move.z;

			ApplyExtraTurnRotation();

			// control and velocity handling is different when grounded and airborne:
			if (m_IsGrounded)
			{
				HandleGroundedMovement(crouch, jump);
				//PlayEffect(audioName);
			}
			else
			{
				HandleAirborneMovement();
			}

			ScaleCapsuleForCrouching(crouch);
			PreventStandingInLowHeadroom();

			// send input and other state parameters to the animator
			UpdateAnimator(move);
		}


		void ScaleCapsuleForCrouching(bool crouch)
		{
			if (m_IsGrounded && crouch)
			{
				if (m_Crouching) return;
				m_Capsule.height = m_Capsule.height / 2f;
				m_Capsule.center = m_Capsule.center / 2f;
				m_Crouching = true;
			}
			else
			{
				Ray crouchRay = new Ray(m_Rigidbody.position + Vector3.up * m_Capsule.radius * k_Half, Vector3.up);
				float crouchRayLength = m_CapsuleHeight - m_Capsule.radius * k_Half;
				if (Physics.SphereCast(crouchRay, m_Capsule.radius * k_Half, crouchRayLength, Physics.AllLayers, QueryTriggerInteraction.Ignore))
				{
					m_Crouching = true;
					return;
				}
				m_Capsule.height = m_CapsuleHeight;
				m_Capsule.center = m_CapsuleCenter;
				m_Crouching = false;
			}
		}

		void PreventStandingInLowHeadroom()
		{
			// prevent standing up in crouch-only zones
			if (!m_Crouching)
			{
				Ray crouchRay = new Ray(m_Rigidbody.position + Vector3.up * m_Capsule.radius * k_Half, Vector3.up);
				float crouchRayLength = m_CapsuleHeight - m_Capsule.radius * k_Half;
				if (Physics.SphereCast(crouchRay, m_Capsule.radius * k_Half, crouchRayLength, Physics.AllLayers, QueryTriggerInteraction.Ignore))
				{
					m_Crouching = true;
				}
			}
		}


		void UpdateAnimator(Vector3 move)
		{
			// update the animator parameters
			m_Animator.SetFloat("Forward", m_ForwardAmount, 0.1f, Time.deltaTime);
			m_Animator.SetFloat("Turn", m_TurnAmount, 0.1f, Time.deltaTime);
			m_Animator.SetBool("Crouch", m_Crouching);
			m_Animator.SetBool("OnGround", m_IsGrounded);
			if (!m_IsGrounded)
			{
				m_Animator.SetFloat("Jump", m_Rigidbody.velocity.y);
			}

			// calculate which leg is behind, so as to leave that leg trailing in the jump animation
			// (This code is reliant on the specific run cycle offset in our animations,
			// and assumes one leg passes the other at the normalized clip times of 0.0 and 0.5)
			float runCycle =
				Mathf.Repeat(
					m_Animator.GetCurrentAnimatorStateInfo(0).normalizedTime + m_RunCycleLegOffset, 1);
			float jumpLeg = (runCycle < k_Half ? 1 : -1) * m_ForwardAmount;
			if (m_IsGrounded)
			{
				m_Animator.SetFloat("JumpLeg", jumpLeg);
			}

			// the anim speed multiplier allows the overall speed of walking/running to be tweaked in the inspector,
			// which affects the movement speed because of the root motion.
			if (m_IsGrounded && move.magnitude > 0)
			{
				m_Animator.speed = m_AnimSpeedMultiplier;
			}
			else
			{
				// don't use that while airborne
				m_Animator.speed = 1;
			}
		}


		void HandleAirborneMovement()
		{
			// apply extra gravity from multiplier:
			Vector3 extraGravityForce = (Physics.gravity * m_GravityMultiplier) - Physics.gravity;
			m_Rigidbody.AddForce(extraGravityForce);

			m_GroundCheckDistance = m_Rigidbody.velocity.y < 0 ? m_OrigGroundCheckDistance : 0.01f;
		}


		void HandleGroundedMovement(bool crouch, bool jump)
		{
			// check whether conditions are right to allow a jump:
			if (jump && !crouch && m_Animator.GetCurrentAnimatorStateInfo(0).IsName("Grounded"))
			{
				// jump!
				m_Rigidbody.velocity = new Vector3(m_Rigidbody.velocity.x, m_JumpPower, m_Rigidbody.velocity.z);
				m_IsGrounded = false;
				m_Animator.applyRootMotion = false;
				m_GroundCheckDistance = 0.1f;
			}
		}

		void ApplyExtraTurnRotation()
		{
			// help the character turn faster (this is in addition to root rotation in the animation)
			float turnSpeed = Mathf.Lerp(m_StationaryTurnSpeed, m_MovingTurnSpeed, m_ForwardAmount);
			transform.Rotate(0, m_TurnAmount * turnSpeed * Time.deltaTime, 0);
		}


		public void OnAnimatorMove()
		{
			// we implement this function to override the default root motion.
			// this allows us to modify the positional speed before it's applied.
			if (m_IsGrounded && Time.deltaTime > 0)
			{
				Vector3 v = (m_Animator.deltaPosition * m_MoveSpeedMultiplier) / Time.deltaTime;

				// we preserve the existing y part of the current velocity.
				v.y = m_Rigidbody.velocity.y;
				m_Rigidbody.velocity = v;
			}
		}


		void CheckGroundStatus()
		{
			RaycastHit hitInfo;
#if UNITY_EDITOR
			// helper to visualise the ground check ray in the scene view
			Debug.DrawLine(transform.position + (Vector3.up * 0.1f), transform.position + (Vector3.up * 0.1f) + (Vector3.down * m_GroundCheckDistance));
#endif
			// 0.1f is a small offset to start the ray from inside the character
			// it is also good to note that the transform position in the sample assets is at the base of the character
			if (Physics.Raycast(transform.position + (Vector3.up * 0.1f), Vector3.down, out hitInfo, m_GroundCheckDistance))
			{
				m_GroundNormal = hitInfo.normal;
				m_IsGrounded = true;
				m_Animator.applyRootMotion = true;
			}
			else
			{
				m_IsGrounded = false;
				m_GroundNormal = Vector3.up;
				m_Animator.applyRootMotion = false;
			}
		}
	}
}

// This code is used to control the movement and animation of the third person character. It contains a class called "ThirdPersonCharacter", which inherits from MonoBehaviour.

// The character's movement is controlled using the Arduino. It communicates with the Arduino via the serial port and receives bytes of data sent from the Arduino. Based on the received bytes, the direction of horizontal and vertical movement of the character is determined. The Move() method is then called to move the character based on the parameters of the direction of movement, whether to crouch and whether to jump.

// The Move() method handles the character's movement and animation control based on the character's direction of movement and other parameters. It converts the move vector in the world coordinate system to a steering and forward vector in the local coordinate system, and applies additional steering rotations. Then, depending on whether the character is on the ground, ground movement or air movement is handled. Finally, the animation parameters are updated to reflect the character's movement state.

// The script also contains a number of auxiliary methods, such as scaling the character's capsule collider to fit a crouched state, preventing the character's head from hitting a low ceiling, and detecting whether the character is on the ground.
```


### Generation of labyrinth floor tiles
```ruby
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using DG.Tweening;
using UnityEngine.SceneManagement;
using UnityStandardAssets.Characters.ThirdPerson;
using UnityEngine.UI;

public class PlayerControl : MonoBehaviour
{

    public GameObject[] allCell; // An array of all cell game objects

    public GameObject[] gameOverPlane; // Array of end-of-game screens

    public ThirdPersonUserControl thirdPersonUser;

    public Text textTime; // Text component showing time


    // Start is called before the first frame update
    IEnumerator Start()
    {


        Time.timeScale = 1;
        textTime.text = "10";
        yield return new WaitForSeconds(1f);
        textTime.text = "9";
        yield return new WaitForSeconds(1f);
        textTime.text = "8";
        yield return new WaitForSeconds(1f);
        textTime.text = "7";
        yield return new WaitForSeconds(1f);
        textTime.text = "6";
        yield return new WaitForSeconds(1f);
        textTime.text = "5";
        yield return new WaitForSeconds(1f);
        textTime.text = "4";
        yield return new WaitForSeconds(1f);
        textTime.text = "3";
        yield return new WaitForSeconds(1f);
        textTime.text = "2";
        yield return new WaitForSeconds(1f);
        textTime.text = "1";
        yield return new WaitForSeconds(1f);
        textTime.text = "0";
        textTime.gameObject.SetActive(false);
        for (var i = 0; i < allCell.Length; i++) {

            allCell[i].gameObject.GetComponent<MeshRenderer>().material.DOFade(0, 1f);
        }
        thirdPersonUser.enabled = true;

        // waiting 1 second at a time and updating the time the text is displayed
        // Finally, hide the text, fade the materials of all cellular game objects and enable the third person character controller
    }

    // Update is called once per frame
    void Update()
    {
        
    }

    // Collision event handling
    public void OnCollisionEnter(Collision collision)
    {
        if (collision.gameObject.tag == "Cell") 
        {
            // If the colliding game object is a cell, fade its material

            collision.gameObject.GetComponent<MeshRenderer>().material.DOFade(1, 1f);
        }

        if (collision.gameObject.tag == "Plane")
        {
            // If the colliding game object is a Plane labeled object, display the game over screen 1 and pause the game

            gameOverPlane[0].SetActive(true);
            Time.timeScale = 0;
        }

        if (collision.gameObject.tag == "Door")
        {
            // If the object collided with is a Door labeled object, display the game over screen 2 and pause the game

            gameOverPlane[1].SetActive(true);
            Time.timeScale = 0;
        }
    }


    // Method of loading scenes
    public void LoadSence(int value) {
        SceneManager.LoadScene(value);
    
    }
}


// In this script, the allCell array stores all the cell game objects, the gameOverPlane array stores the end-of-game interface objects, thirdPersonUser is the third-person character controller and textTime is the text component used to display the time.

// In the Start() method, a countdown effect is implemented using a concurrent thread. The time displayed for the text is updated second by second, and at the end of the countdown the text is hidden, the materials of all cellular game objects are faded, and the third person character controller is enabled.

// In the OnCollisionEnter() method, player collision events with other game objects are handled. If a collision is made with a Cell object, its material is faded. If a Plane tagged object is collided with, the Game Over screen1 is displayed and the game is paused. If a Door-labelled object is collided with, the Game Over screen 2 is displayed and the game is paused.

// The LoadScene() method is used to load the scene, loading the corresponding scene based on the values passed in.

// In the Update() method, additional logic or controls can be added to handle each frame's actions.
```

### Game level settings

Write the floor moving code to make the different settings for each level possible
```ruby
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using DG.Tweening;
public class MoveWay : MonoBehaviour
{
    public Transform[] targetTf; // Transform array for storing path points
    public int curIndex;   // Index of the current path point
    public float speedTimer = 2f; // Timer for movement speed

    public List<Vector3> allPos = new List<Vector3>(); // Store the location information of all path points
    // Start is called before the first frame update
    void Start()
    {
        // Add the location information of the path points to the allPos list
        for (var i = 0; i < targetTf.Length; i++) {
            allPos.Add(targetTf[i].position);
        }

        // Start moving to the next path point
        NextMove();
    }


    // Switch to the next path point
    public void NextMove() {
        curIndex++;

        // If the current index exceeds the number of path points, reset the index to 0 and start from the beginning
        if (curIndex >= allPos.Count)
            curIndex = 0;

            // Use the DOTween plugin to smoothly move objects to the next path point
        transform.DOMove(allPos[curIndex], speedTimer).SetEase(Ease.Linear).OnComplete(()=> {

            NextMove();
        });
    }
    // Update is called once per frame
    void Update()
    {
        
    }
}


// In this script, the targetTf array stores the Transform component of the path point. In the Start() method, the position information of the path point is added to the allPos list. The NextMove() method is then called to start moving the object to the next path point.

// The NextMove() method first increments the value of curIndex and then checks to see if the number of path points is exceeded. If it does, the index is reset to 0 and the move starts from the beginning. Then the object is moved smoothly to the next path point position using the transform.DOMove() method and the DOTween plug-in. The time to move and the jog function are set. Once the move is complete, the NextMove() method is called recursively using the OnComplete() method to achieve a circular move.

// In the Update() method, additional logic or controls can be added to handle each frame of the operation
```

