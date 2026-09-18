# Joystick-LED-Control
Arduino Code to control the LED's placed on a breadboard through a joystick


// joystick variables
int vrx = A0;
int vry = A1;
int sw = 8; // button on the joystick

int sw_val;
int x_val;
int y_val;

// LED variables
int fblue = 2;
int sblue = 3;
int fgreen = 4;
int sgreen = 5;
int fyellow = 6;
int syellow = 7;

// Variable for remembering whether the system is on
int i = 0;

// Keeps track of which LED is selected
int j = 0;

// Used to detect a new button press
int lastButtonState = HIGH;

// Used so one joystick movement only changes once
bool joystickReady = true;

// Put all LED pins into an array
int leds[] = {
  fblue,
  sblue,
  fgreen,
  sgreen,
  fyellow,
  syellow
};

void setup() {
  Serial.begin(9600);

  pinMode(vrx, INPUT);
  pinMode(vry, INPUT);
  pinMode(sw, INPUT_PULLUP);

  pinMode(fblue, OUTPUT);
  pinMode(sblue, OUTPUT);
  pinMode(fgreen, OUTPUT);
  pinMode(sgreen, OUTPUT);
  pinMode(fyellow, OUTPUT);
  pinMode(syellow, OUTPUT);

  // Start with every LED off
  for (int k = 0; k < 6; k++) {
    digitalWrite(leds[k], LOW);
  }
}

void loop() {

  // Read joystick values
  x_val = analogRead(vrx);
  y_val = analogRead(vry);
  sw_val = digitalRead(sw);

  // Print values to Serial Monitor
  Serial.print("X Value: ");
  Serial.println(x_val);

  Serial.print("Y Value: ");
  Serial.println(y_val);

  Serial.print("Button State: ");
  Serial.println(sw_val);
  

  // Turn system on or off when button is pressed
  if (sw_val == LOW && lastButtonState == HIGH) {

   // If system is off, turn it on
    if (i == 0) {
      i = 1;
      j = 0;

   // Turn on the first blue LED
      digitalWrite(leds[j], HIGH);
    }
    // If system is on, turn it off
    else {
      i = 0;
      // Turn off every LED
      for (int k = 0; k < 6; k++) {
        digitalWrite(leds[k], LOW);
      }
    }
    delay(200); // Button debounce
  }




  // Only allow joystick movement when system is on
  if (i == 1) {
    // Move joystick right
    if (x_val > 720 && joystickReady == true) {
      // Turn current LED off
      digitalWrite(leds[j], LOW);
      // Move to the next LED
      j++;
      // Go back to the first LED after the last LED
      if (j > 5) {
        j = 0;
      }
      // Turn new LED on
      digitalWrite(leds[j], HIGH);
      // Prevent repeated movement while joystick is held
      joystickReady = false;
    }


   // Move joystick left
    if (x_val < 300 && joystickReady == true) {
      // Turn current LED off
      digitalWrite(leds[j], LOW);
      // Move to the previous LED
      j--;
      // Go to the last LED if moving left from the first LED
      if (j < 0) {
        j = 5;
      }

  // Turn new LED on
      digitalWrite(leds[j], HIGH);

  // Prevent repeated movement while joystick is held
      joystickReady = false;
    }


  // Reset joystick when it returns to the center
    if (x_val > 400 && x_val < 600) {
      joystickReady = true;
    }
  }

  delay(50);
}
