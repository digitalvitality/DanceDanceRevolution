# DanceDanceRevolution
This will hold all of my resources for recreating a DDR Metal dance pad ( tournament pad ) including recreated original jpg designs.

These are non original high res recreations I found online: The serve fine for recreation if you aren't looking for originality.

![hires](https://github.com/user-attachments/assets/2467e21f-b733-4dd7-8640-b58cbe2246ae)

Below are slices of the necessary left and right SHOW ME YOUR MOVES! Buttons

![try me boi adjusted printable](https://github.com/user-attachments/assets/6c22e620-228b-4336-b6d3-5ad20fd78959)

![show me your moves right](https://github.com/user-attachments/assets/3a782134-3605-4961-8506-6d77752654db) ![show me your moves left](https://github.com/user-attachments/assets/bbd3be5d-1ce0-4bd1-91cc-d964f9c431b0)

Basic connection method:

Foil Contact Sensors (DIY)
Place aluminum foil strips under each panel.
Use a non-conductive spacer (foam or rubber pads) to separate them.
When you step, the foil touches, completing the circuit.
Wire each foil sensor to the PS2 controller PCB.

Connect to a PS2
Using a PS2 Controller PCB (Easiest DIY Method)
Get a PS2 controller (official or third-party).
Disassemble the controller to access the PCB.
Identify the D-pad contacts (Up, Down, Left, Right) and the Ground wire (GND).
Solder wires from the pad sensors to the corresponding D-pad contacts.
Reassemble the PCB inside the pad or in an external box.
Use a standard PS2 controller cable to connect to the console.

Final Testing & Tweaks
Test button responsiveness in a PS2 DDR game (e.g., DDR Extreme).
Adjust panel sensitivity by modifying sensor tension or switch placement.
Add anti-slip materials underneath the pad.
Bonus Features (Optional)
LED lighting under arrows (use 5V LEDs powered by USB or an external source).
USB compatibility (add a PS2-to-USB adapter for PC compatibility).
Custom artwork (print and place decals under clear acrylic panels).
Alternative: Using an Arduino Instead of a PS2 PCB
Instead of a PS2 controller PCB, you can use an Arduino with a PS2 interface module to send inputs.
This allows for more customization, but requires programming.
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

PREFERRED Method:
The PlayStation 2 controller communicates using a SPI-like protocol over the 9-pin PS2 connector. The important pins are:

Pin 1: Data (DATA) – Sends button states to the console
Pin 2: Command (CMD) – PS2 asks for button states
Pin 3: Ground (GND)
Pin 4: Power (VCC, 3.3V)
Pin 6: Clock (CLK) – Synchronization
Pin 7: Attention (ATT) – Signals active communication
The Arduino will emulate a PS2 controller, interpreting pad inputs and sending the correct signals.

Matierals needed:
Arduino Nano
PS2 extension cable (cut open to access wires)
Pushbuttons, foil sensors, or microswitches (for the pad inputs)
Wires & soldering tools

Wiring the DDR Pad to the Arduino
Each arrow panel on the DDR pad needs a sensor:

Option 1: Mechanical switches (arcade buttons or microswitches)

Option 2: Conductive foil sensors (DIY contact sensors)
Connect DDR Pad Sensors to Arduino

DDR Button	Arduino Pin
Up	           D2
Down           D3
Left	         D4
Right	         D5
Start	         D6
Select	       D7
Each button connects to an Arduino digital pin with a pull-down resistor (10KΩ) to keep inputs stable.

You'll use the PS2 protocol to send signals:

PS2 Pin	    Function	         Arduino Pin
1           	DATA	            D9 (MISO)
2            	CMD	              D11 (MOSI)
4	            VCC (3.3V)	      3.3V
6	            CLK	              D13 (SCK)
7	            ATT	              D10 (CS)
3	            GND      	        GND
Use a logic level shifter (or resistor dividers) since the PS2 operates at 3.3V logic while the Nano use 5v (or use a qtpy which operates at 3.3)

The Arduino needs to:

Read inputs from DDR sensors
Convert inputs into PS2 controller signals
Send signals to the PS2 console
You'll need to use a PS2 Controller Emulation Library (such as the PS2X library).



Code base below
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
#include <PS2X_lib.h>

PS2X ps2x;  // Create PS2 controller object

#define UP_PIN 2
#define DOWN_PIN 3
#define LEFT_PIN 4
#define RIGHT_PIN 5
#define START_PIN 6
#define SELECT_PIN 7

void setup() {
    Serial.begin(115200);
    
    // DDR Pad Inputs as Pull-up
    pinMode(UP_PIN, INPUT_PULLUP);
    pinMode(DOWN_PIN, INPUT_PULLUP);
    pinMode(LEFT_PIN, INPUT_PULLUP);
    pinMode(RIGHT_PIN, INPUT_PULLUP);
    pinMode(START_PIN, INPUT_PULLUP);
    pinMode(SELECT_PIN, INPUT_PULLUP);

    // Initialize PS2 controller emulation
    ps2x.config_gamepad(10, 11, 9, 13, false, false); // (ATT, CMD, DATA, CLK, Pressure, Rumble)
}

void loop() {
    ps2x.read_gamepad(false, false);  // Read gamepad state

    // DDR Button Mapping
    if (digitalRead(UP_PIN) == LOW) ps2x.setButtonPress(PSB_PAD_UP, 255);
    if (digitalRead(DOWN_PIN) == LOW) ps2x.setButtonPress(PSB_PAD_DOWN, 255);
    if (digitalRead(LEFT_PIN) == LOW) ps2x.setButtonPress(PSB_PAD_LEFT, 255);
    if (digitalRead(RIGHT_PIN) == LOW) ps2x.setButtonPress(PSB_PAD_RIGHT, 255);
    if (digitalRead(START_PIN) == LOW) ps2x.setButtonPress(PSB_START, 255);
    if (digitalRead(SELECT_PIN) == LOW) ps2x.setButtonPress(PSB_SELECT, 255);

    delay(10); // Small delay for stability
}
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Upload the code to the Arduino Nano.
Connect the DDR pad to the PS2.
Run a game like DDR Extreme on the PS2.
Test each arrow panel and button.
If inputs aren’t working:
Check wiring connections.
Verify Arduino is outputting correct signals.
Ensure PS2 communication logic is correct.
