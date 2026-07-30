# Gesture-controlled Robot
Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails!

You should comment out all portions of your portfolio that you have not completed yet, as well as any instructions:
```HTML 
<!--- This is an HTML comment in Markdown -->
<!--- Anything between these symbols will not render on the published site -->
```

| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Zachary C | West Essex High School | Electrical Engineering | Incoming Senior

**Replace the BlueStamp logo below with an image of yourself and your completed project. Follow the guide [here](https://tomcam.github.io/least-github-pages/adding-images-github-pages-site.html) if you need help.**

![Headstone Image](logo.svg)
  
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE



# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/rWl_ldPpumk?si=gLNwY2qevSM6LrCk" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Summary

The second milestone consisted of connecting and configuring the HC05 Bluetooth modules, as well as transmitting accelerometer data to move the robot in certain directions. The first step to configuring the Bluetooth modules was to get them in AT mode, where they can receive AT commands. From there, I input various commands to make the Nano's HC05 the sender, and the Uno's HC05 the receiver. To pair them, I used an AT+ADDR? to find the specific address of the receiver, and an AT+PAIR with that address to pair them. Next, I set them to communicate on the same UART baud rate of 38400. Once they were configured, I tested whether they were actually communicating by sending data from the Nano's HC05 to the Uno's HC05 and observing if the data appeared on the Uno's Serial Monitor. Moving on to the accelerometer, the wiring was relatively simple. The accelerometer measures tilt, orientation, and acceleration. These values are all transmitted to an Arduino Nano, which sends the data through the HC05 Bluetooth module to the receiver on the robot. The Arduino Uno reads these values, and if they pass certain thresholds, it will signal the motor drivers to power specific motors, moving the robot in various directions.

### Challenges

While configuring and connecting the HC05 Bluetooth Modules was not too difficult, I had issues with actually getting them to communicate. The problem I kept hitting was that the Nano would be sending messages, but the Uno would not be receiving any data. The issue lay in the fact that the Arduino Nano 33 BLE Sense that I received did not output 5V by default; it output 3.3V. Thinking that it output 5V, I wired a voltage divider between my Nano's TX pin and the HC05's RXD pin so that the RXD pin would receive 3.3V, thus avoiding frying the chip. However, since the Nano was already outputting 3.3V, I was lowering the voltage below the threshold needed for the HC05 to work. The simple fix was to just get rid of the voltage divider and wire the Nano's TX pin directly to the HC05's RXD pin. I also encountered a debounce issue with the accelerometer: the signal fluctuated near the threshold angle, causing erratic movement from rapid, opposite-direction commands. Adding a simple debounce timer in the code fixed this by ignoring direction signals that changed too quickly.

# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/ZgMsRAHnwXw?si=bXjg2w3ZC6Q_6f5u" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" referrerpolicy="strict-origin-when-cross-origin" allowfullscreen></iframe>

### Summary

My first milestone focused on building the robot and wiring it together. I screwed each motor into the chassis's bottom frame to ensure a stable, secure hold. Then I connected each of the four motors to a motor driver, which was then wired to an Arduino Uno. The Arduino Uno served as the controller for the motors, and the motor driver bridged the Arduino to the gearbox motors. I then powered the motors by directly connecting a 9V battery to a breadboard's power rails, then using male-to-female jumper wires to connect the breadboard power rails to the motor driver's power input. All of the components on the robot were secured through screws or Velcro tape. I attached the top of the robot chassis and powered the Arduino with another 9V battery, this time using a barrel connector. The robot chassis build tutorial included a file that I downloaded and uploaded to the Arduino. This code was used to test the motors, ensuring that they can be powered synchronously, moving the robot forward, backward, left, and right.

### Challenges

The motors require a lot of power to run all at once, and the breadboard power supply could only supply a maximum of 5V. To ensure that all four motors receieved enough power, all 9V needed to be utilized from the battery. So I cut off the barrel connecter on the battery attachment and stripped the wires to expose the stranded copper wire. I then soldered the stranded wires together so that they can be easily inserted into the breadboard power rails, then I wired it to the motor driver to power all of the motors.

# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 

# Code
Here's where you'll put your code. The syntax below places it into a block of code. Follow the guide [here]([url](https://www.markdownguide.org/extended-syntax/)) to learn how to customize it to your project needs. 

## [Camera Web Server Code](CameraWebServer/)

## Testing HC05 Communication Code

### Arduino Nano BLE 33 Sense - Sender

```c++
#define BT Serial1   // HC-05 connected to RX1/TX1 (D0/D1)

unsigned long lastSend = 0;
int counter = 0;

void setup() {
  Serial.begin(9600);   // USB serial for debugging
  while (!Serial) { }     // wait for Serial Monitor

  BT.begin(38400);        // must match AT+UART=38400,0,0

  Serial.println("Nano 33 BLE Sense sender ready");
}

void loop() {
  // Send a test message every 1000 ms
  if (millis() - lastSend >= 1000) {
    lastSend = millis();

    BT.print("MSG ");
    BT.print(counter++);
    BT.print(" TIME ");
    BT.println(millis());

    Serial.println("Sent message");
  }

  // Echo anything received from the UNO back to the Serial Monitor
  while (BT.available()) {
    char c = BT.read();
    Serial.write(c);
  }
}
```

### Arduino Uno - Receiver

```c++
#include <SoftwareSerial.h>

// HC-05 connections on the UNO
// HC-05 TXD -> D2 (UNO RX)
// HC-05 RXD <- D3 (UNO TX through 5V->3.3V divider)
SoftwareSerial BT(2, 3); // RX, TX

void setup() {
  Serial.begin(9600);  // USB Serial Monitor
  BT.begin(38400);       // must match AT+UART=38400,0,0

  Serial.println("UNO receiver ready");
}

void loop() {
  // Print any data received from the Nano
  while (BT.available()) {
    char c = BT.read();
    Serial.write(c);
  }

  // Optional: type in Serial Monitor and forward to Nano
  while (Serial.available()) {
    char c = Serial.read();
    BT.write(c);
  }
}
```

## Code for Configuring HC05 Bluetooth Modules

```c++
void setup() {
  Serial.begin(9600);

  // Wait for Serial Monitor
  while (!Serial);

  // HC-05 AT mode baud rate
  Serial1.begin(38400);

  Serial.println("HC-05 AT Mode Test");
  Serial.println("Type AT commands below:");
}

void loop() {
  // Send Serial Monitor input to HC-05
  while (Serial.available()) {
    Serial1.write(Serial.read());
  }

  // Send HC-05 response to Serial Monitor
  while (Serial1.available()) {
    Serial.write(Serial1.read());
  }
}
```

## Code to Test Robot Motors

```c++
 * After running the code, smart car will go forward 2 seconds, then go backward 2
 * seconds, then left turn for 2 seconds then right turn for 2 seconds then stop. 
 * 
 */
#define speedPinR 9           //  RIGHT PWM pin connect MODEL-X ENA
#define RightMotorDirPin1 12  //Right Motor direction pin 1 to MODEL-X IN1
#define RightMotorDirPin2 11  //Right Motor direction pin 2 to MODEL-X IN2
#define speedPinL 6           // Left PWM pin connect MODEL-X ENB
#define LeftMotorDirPin1 7    //Left Motor direction pin 1 to MODEL-X IN3
#define LeftMotorDirPin2 8    //Left Motor direction pin 1 to MODEL-X IN4


/*motor control*/
void go_Advance(void)  //Forward
{
  digitalWrite(RightMotorDirPin1, HIGH);
  digitalWrite(RightMotorDirPin2, LOW);
  digitalWrite(LeftMotorDirPin1, HIGH);
  digitalWrite(LeftMotorDirPin2, LOW);
  analogWrite(speedPinL, 200);
  analogWrite(speedPinR, 200);
}
void go_Left(int t = 0)  //Turn left
{
  digitalWrite(RightMotorDirPin1, HIGH);
  digitalWrite(RightMotorDirPin2, LOW);
  digitalWrite(LeftMotorDirPin1, LOW);
  digitalWrite(LeftMotorDirPin2, HIGH);
  analogWrite(speedPinL, 200);
  analogWrite(speedPinR, 200);
  delay(t);
}
void go_Right(int t = 0)  //Turn right
{
  digitalWrite(RightMotorDirPin1, LOW);
  digitalWrite(RightMotorDirPin2, HIGH);
  digitalWrite(LeftMotorDirPin1, HIGH);
  digitalWrite(LeftMotorDirPin2, LOW);
  analogWrite(speedPinL, 200);
  analogWrite(speedPinR, 200);
  delay(t);
}
void go_Back(int t = 0)  //Reverse
{
  digitalWrite(RightMotorDirPin1, LOW);
  digitalWrite(RightMotorDirPin2, HIGH);
  digitalWrite(LeftMotorDirPin1, LOW);
  digitalWrite(LeftMotorDirPin2, HIGH);
  analogWrite(speedPinL, 200);
  analogWrite(speedPinR, 200);
  delay(t);
}
void stop_Stop()  //Stop
{
  digitalWrite(RightMotorDirPin1, LOW);
  digitalWrite(RightMotorDirPin2, LOW);
  digitalWrite(LeftMotorDirPin1, LOW);
  digitalWrite(LeftMotorDirPin2, LOW);
}
/*set motor speed */
void set_Motorspeed(int speed_L, int speed_R) {
  analogWrite(speedPinL, speed_L);
  analogWrite(speedPinR, speed_R);
}

//Pins initialize
void init_GPIO() {
  pinMode(RightMotorDirPin1, OUTPUT);
  pinMode(RightMotorDirPin2, OUTPUT);
  pinMode(speedPinL, OUTPUT);

  pinMode(LeftMotorDirPin1, OUTPUT);
  pinMode(LeftMotorDirPin2, OUTPUT);
  pinMode(speedPinR, OUTPUT);
  stop_Stop();
}

void setup() {
  init_GPIO();

  go_Advance();  //Forward

  delay(2000);

  go_Back();  //Reverse

  delay(2000);

  go_Left();  //Turn left

  delay(2000);

  go_Right();  //Turn right

  delay(2000);

  stop_Stop();  //Stop
}

void loop() {
}
```

# Bill of Materials

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| Car Chassis Kit | Used as the body of the robot | $39.99 | <a href="https://www.amazon.com/dp/B0DJ7BT1V5?lv=shuf&rsd=oU5zjHTwUjufNpZkC6CW0sqRlEipy6Xgf59f5777Kxh7cknbp6DwTNVEgVR1R1/Y0I8OXRT9EOeWKVF0ff4yEbtnF/c9MNo6yf5KfYW6Lx+kqE4=&edk=AQIDAHi1lw/M8UbbSMD9ScOOFEmBMHMthHeEhqDaQYPJUAX3jQHYb0B2nFfwd4jzBFZyiYMUAAAAfjB8BgkqhkiG9w0BBwagbzBtAgEAMGgGCSqGSIb3DQEHATAeBglghkgBZQMEAS4wEQQM7ULhz148q+1PjBJVAgEQgDvE8maRGRFUIB7tnUdXxocbXxxr5gXUvho7mquZi7Zok3ViYk7wwVFTYIEajFhVByN74efn2RX1qaf+HQ==&social_share=cm_sw_r_cso_cp_apin_dp_RCSYWRX92M0H5DJ6HNQA&channelId=704&ref_=cm_sw_r_cso_cp_apin_dp_RCSYWRX92M0H5DJ6HNQA&plpRedirect=mhFallback"> Link </a> |
| Screwdriver Kit | Tools required for assembly | $5.94 | <a href="https://www.amazon.com/Small-Screwdriver-Set-Mini-Magnetic/dp/B08RYXKJW9/"> Link </a> |
| Arduino Uno Clone | Microcontroller used to receive and process inputs/outputs | $14.98 | <a href="https://www.amazon.com/ELEGOO-Board-ATmega328P-ATMEGA16U2-Compliant/dp/B01EWOE0UU/ref=sr_1_2_sspa?crid=3A6NCD2X9JEMJ&dib=eyJ2IjoiMSJ9.AcWZy-Yg4mDTnhzEHozxzPZdVC5-KUL2tW-OQewDKpBB4brSpD-p4bn74WcXiW3KarYertgpNaLJ0VHKx0qsPqolKAhiz1GRG5BwJQl73cEvrlXIXNmqlpSvU7uu2aRVSwAZi9Gj2AjSPLM3esW1Gzy9xEiQ9oiR5LCNjh4MlYDx5mTm5sI4rsD4CFTipJnF572qXlickl35FRcCj8oMXQotumgqI4yEIq0HobOtIlEnNhtVB51JMBHhqtmmF_PC9WeHJ4ySUVVcv_gq3_VeG1aAEbdm4NXmmT6NOYPw4Qo.1PFdgFT22oqO5Mg6-6j_aUL_EV8tUPuaFrB5N9oaEX0&dib_tag=se&keywords=elegoo+arduino&qid=1716856465&s=electronics&sprefix=elegoo+arduino%2Celectronics%2C99&sr=1-2-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1"> Link </a> |
| Electronics Kit | Includes jumper wires, various smaller components like potentiometers, buzzers, resistors, LEDs, etc. | $14 | <a href="https://www.amazon.com/Smraza-Electronics-Potentiometer-tie-Points-Breadboard/dp/B0B62RL725/ref=sxts_b2b_sx_reorder_acb_business?content-id=amzn1.sym.f63a3b0b-3a29-4a8e-8430-073528fe007f%3Aamzn1.sym.f63a3b0b-3a29-4a8e-8430-073528fe007f&crid=2IC3T44H3U3WG&cv_ct_cx=breadboard+kit&dib=eyJ2IjoiMSJ9.TUd5tu2T8rmms7ZuJ0UzmbtpLL1zsu93bQM0PzwnP4E.sT0V0vL_QtbYv8ymVTCcRkhFNgBtRvRiT7G4FT1oGTE&dib_tag=se&keywords=breadboard+kit&pd_rd_i=B0B62RL725&pd_rd_r=67e1f4ff-e3b9-44e4-b441-b4ae282f036b&pd_rd_w=UjFaP&pd_rd_wg=0xRoC&pf_rd_p=f63a3b0b-3a29-4a8e-8430-073528fe007f&pf_rd_r=BFGP77H27ZN31W4PZAW6&qid=1715911733&sbo=RZvfv%2F%2FHxDF%2BO5021pAnSA%3D%3D&sprefix=breadboard+kit%2Caps%2C109&sr=1-2-9f062ed5-8905-4cb9-ad7c-6ce62808241a"> Link </a> |
| Breadboard Kit | Allows for solderless connections | $8.79 | <a href="https://www.amazon.com/Breadboards-Solderless-Breadboard-Distribution-Connecting/dp/B07DL13RZH/ref=sxts_b2b_sx_reorder_acb_business?content-id=amzn1.sym.f63a3b0b-3a29-4a8e-8430-073528fe007f%3Aamzn1.sym.f63a3b0b-3a29-4a8e-8430-073528fe007f&crid=1RAL6PA1TZ81Q&cv_ct_cx=breadboard+kit&dib=eyJ2IjoiMSJ9.TUd5tu2T8rmms7ZuJ0UzmbtpLL1zsu93bQM0PzwnP4E.sT0V0vL_QtbYv8ymVTCcRkhFNgBtRvRiT7G4FT1oGTE&dib_tag=se&keywords=breadboard+kit&pd_rd_i=B07DL13RZH&pd_rd_r=1e3e6f57-5578-4452-b230-90d43c79b5d3&pd_rd_w=rFN6B&pd_rd_wg=3mMuA&pf_rd_p=f63a3b0b-3a29-4a8e-8430-073528fe007f&pf_rd_r=JC9D7T4VYRDQ9HJVY5X8&qid=1715912837&s=electronics&sbo=RZvfv%2F%2FHxDF%2BO5021pAnSA%3D%3D&sprefix=breadboard+kit%2Celectronics%2C102&sr=1-1-9f062ed5-8905-4cb9-ad7c-6ce62808241a"> Link </a> |
| Arduino Nano 33 BLE Sense | Collects and transmits sensor data through bluetooth | $39.7 | <a href="https://www.amazon.com/Arduino-Nano-Sense-headers-ABX00070/dp/B0BQHZ88WD/ref=sr_1_4?crid=1BTYPUQCTIWYN&dib=eyJ2IjoiMSJ9.5ykyUyT10Vdnbme1Ur85NoPh9YmzeyxQKWTP0jF0ju7Zw9b2hLtWjY3pTyREGe5HkneZz75CgR3J9S8HJbMwmvkj1c1Mu9x0rZ651S1aBHwNqxIYbKjWG8yzYzDh5tcKP57E9RxRmqavQMCJ-QtCLIFas8oQKdBZDx67b_JUYJ3hdfDjHDXrimHAEzVTZrVAwh6NOXZ8-yMZIcp72LVtDsuQxyCvkrDyZM1EbuZQHlc.iy6QHwMR4-UrQZrInFc0eTSZJP6LewrRVqwpOfrQCG0&dib_tag=se&keywords=arduino+nano+33+ble&qid=1748096993&sprefix=arduino+nano+33ble%2Caps%2C151&sr=8-4"> Link </a> |
| Micro USB Cable | Used to connect the Arduino to the computer | $5 | <a href="https://www.amazon.com/Charging-Transfer-Android-Trustable-MYFON/dp/B098DW7485/ref=sr_1_6?crid=3USJU0DMSZB2S&keywords=micro+usb&qid=1686187078&s=electronics&sprefix=micro+usb%2Celectronics%2C106&sr=1-6"> Link </a> |
| Accelerometer | Acts as an input sensor to translate the hand movements to directional commands | $9 | <a href="https://www.amazon.com/dp/B0D2TJVMNY?ref=fed_asin_title"> Link </a> |
| HC05 | Acts as a wireless serial bridge, sending gesture data from the control glove | $9 | <a href="https://www.amazon.com/DSD-TECH-HC-05-Pass-through-Communication/dp/B01G9KSAF6/ref=sr_1_3?crid=2J833J7AYQJA&keywords=hc05&qid=1686187263&sprefix=hc0%2Caps%2C112&sr=8-3"> Link </a> |
| Breadboard Power Supply | Used to supply power to the breadboards | $8 | <a href="https://www.amazon.com/ALAMSCN-Solderless-Breadboard-Battery-Arduino/dp/B08JYPMCZY/ref=sxts_b2b_sx_reorder_acb_business?content-id=amzn1.sym.f63a3b0b-3a29-4a8e-8430-073528fe007f%3Aamzn1.sym.f63a3b0b-3a29-4a8e-8430-073528fe007f&crid=Z2S8NZU0KN1S&cv_ct_cx=breadboard+power+supply&dib=eyJ2IjoiMSJ9.nJ_euybTOUu9E6yyDpnEqg.NgztCYPGkG96eXyyFxpvxOVw5ykdTUq6oziUQnvf51E&dib_tag=se&keywords=breadboard+power+supply&pd_rd_i=B08JYPMCZY&pd_rd_r=f2beb6df-6d77-44a3-8b72-83255f19ca20&pd_rd_w=r1wmq&pd_rd_wg=ToFNq&pf_rd_p=f63a3b0b-3a29-4a8e-8430-073528fe007f&pf_rd_r=R5ZMMGW4CXRBP3PWAYMA&qid=1715912515&s=electronics&sbo=RZvfv%2F%2FHxDF%2BO5021pAnSA%3D%3D&sprefix=breadboard+power+s%2Celectronics%2C114&sr=1-1-9f062ed5-8905-4cb9-ad7c-6ce62808241a"> Link </a> |
| 9V Batteries | Power Source | $8.69 | <a href="https://www.amazon.com/Amazon-Basics-Performance-All-Purpose-Batteries/dp/B00MH4QM1S/ref=sr_1_5_pp?crid=3TQ7ANPH958JM&dib=eyJ2IjoiMSJ9.bmcV2Upj_vpB6G9CFlPPxYAryat512da7ekZjc52HecXSTmtx7PbJ50EgQFPCMqlAxjOUq-tL4vQTpozlHvH89bMwx-HJoyGcdz6EY8HrMxahTiqOXkoP7ewkDcgHoMhmHamdlQfW6FBHO0Gm-DYZZnnMuvEU3qOpemA8PGEvRhEx4-lGaBZhrvls039G1-9SizAW-YRGXZ2fFrdVDlREyyOhAuxXZaE5QqUxWesRQgP9UfGOYaInRWTTPwhDbXFa-RPzGbU1C_u4wq-NMqKBtWEQqR9-cA8O3FYOx3icEY.dtKJmI2T-iCmMM_bYnbiHUWzhKpJDRxS-bBmZIwYFKM&dib_tag=se&keywords=9v+batteries&qid=1720651326&rdc=1&s=electronics&sprefix=9v+batteries%2Celectronics%2C105&sr=1-5"> Link </a> |
| Velcro Tape | Allows for easy attachment and removal of parts | $8 | <a href="https://www.amazon.com/Art3d-Sticky-Double-Sided-Command-Adhesive/dp/B0B58FGF8H/ref=sr_1_1_sspa?crid=2N0JOMEZLJ2DS&dib=eyJ2IjoiMSJ9.qGUGB_MXfmbL0MW7bqNJbxvZC9pzliDJ9KYyRNNrctnh03kCcUXONRrcPYdGeo7Jwzrm83HyF8Jsb1RkcdlLPAw-8RkxbTCMiW6UI1Fpnjv9GjXUg9VBOLxmLVUbmMp5J7gFXKKLTWQ-w_L4Q9rykEUqKmjv-v6GRykMMZLY2cVt__lLxMIlwr6qBnQLWpHiklifUJwjiURxO--TTt2VReYgmN0z7118ifSucrkvRrg.mwA0L4zMSlJP2RO8IBba7dVqwa1Lkr8KvY1JmeQEfCg&dib_tag=se&keywords=velcro+tape+pieces&qid=1716734034&sprefix=velcro+tape+piece%2Caps%2C89&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1"> Link </a> |
| DMM | Used for debugging and circuit analysis | $9.99 | <a href="https://www.amazon.com/dp/B0CXM242J1?ref=fed_asin_title&th=1"> Link </a> |

# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Robot Chassis Build Tutorial]([https://trashytuber.github.io/YimingJiaBlueStamp/](https://osoyoo.com/2018/12/07/new-smart-car-lesson1/))
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)
