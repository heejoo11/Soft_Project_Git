// Arduino pin assignment
#define PIN_LED  9
#define PIN_TRIG 12   // sonar sensor TRIGGER
#define PIN_ECHO 13   // sonar sensor ECHO

// configurable parameters
#define SND_VEL 346.0     // sound velocity at 24 celsius degree (unit: m/sec)
#define INTERVAL 100       // sampling interval (unit: msec)
#define PULSE_DURATION 10 // ultra-sound Pulse Duration (unit: usec)
#define _DIST_MIN 100.0   // minimum distance (mm)
#define _DIST_MAX 300.0   // maximum distance (mm)

#define TIMEOUT ((INTERVAL / 2) * 1000.0) // maximum echo waiting time (unit: usec)
#define SCALE (0.001 * 0.5 * SND_VEL)     // coefficent to convert duration to distance

unsigned long last_sampling_time;   // unit: msec

void setup() {
  pinMode(PIN_LED, OUTPUT);
  pinMode(PIN_TRIG, OUTPUT);  // sonar TRIGGER
  pinMode(PIN_ECHO, INPUT);   // sonar ECHO
  digitalWrite(PIN_TRIG, LOW);  // turn-off Sonar 
  Serial.begin(57600);
}

void loop() { 
  if (millis() < (last_sampling_time + INTERVAL))
    return;

  float distance = USS_measure(PIN_TRIG, PIN_ECHO); // read distance
  int duty = 255;  // LED PWM duty (0=밝음, 255=꺼짐)

  if ((distance >= _DIST_MIN) && (distance <= _DIST_MAX)) {
    if (distance <= 200.0) {
      // 100mm→255(꺼짐), 150mm→127(중간), 200mm→0(최대 밝기)
      duty = 255 - (int)(255.0 * (distance - 100.0) / 100.0);
    } else {
      // 200mm→0(밝음), 250mm→127(중간), 300mm→255(꺼짐)
      duty = 255 - (int)(255.0 * (300.0 - distance) / 100.0);
    }
  } else {
    duty = 255; // 범위를 벗어나면 LED 꺼짐
  }

  analogWrite(PIN_LED, duty);

  Serial.print("distance: "); Serial.print(distance);
  Serial.print(" mm, duty: "); Serial.println(duty);

  last_sampling_time += INTERVAL;
}

// get a distance reading from USS. return value is in millimeter.
float USS_measure(int TRIG, int ECHO)
{
  digitalWrite(TRIG, HIGH);
  delayMicroseconds(PULSE_DURATION);
  digitalWrite(TRIG, LOW);

  return pulseIn(ECHO, HIGH, TIMEOUT) * SCALE; // unit: mm
}
