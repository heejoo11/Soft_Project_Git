
// Arduino pin assignment
#define PIN_LED  9
#define PIN_TRIG 12
#define PIN_ECHO 13

// configurable parameters
#define SND_VEL 346.0     // sound velocity at 24 celsius degree (unit: m/sec)
#define INTERVAL 100      // sampling interval (unit: msec)
#define PULSE_DURATION 10 // ultra-sound Pulse Duration (unit: usec)
#define _DIST_MIN 100     // minimum distance to be measured (unit: mm)
#define _DIST_MAX 300     // maximum distance to be measured (unit: mm)

#define TIMEOUT ((INTERVAL / 2) * 1000.0)
#define SCALE (0.001 * 0.5 * SND_VEL)

#define N 10 //3, 10, 30으로 변경하며 시리얼 플로터 확인할 것

// 전역 변수
float samples[N];
int sampleIndex = 0;
bool bufferFilled = false;

void setup() {
  Serial.begin(57600);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);
  pinMode(PIN_LED, OUTPUT);
}

float readDistance() {
  digitalWrite(PIN_TRIG, LOW);
  delayMicroseconds(2);
  digitalWrite(PIN_TRIG, HIGH);
  delayMicroseconds(PULSE_DURATION);
  digitalWrite(PIN_TRIG, LOW);

  long duration = pulseIn(PIN_ECHO, HIGH, TIMEOUT);
  float distance = duration * SCALE * 1000.0; // mm 단위
  return distance;
}

float getMedian(float *arr, int size) {
  float sorted[size];
  memcpy(sorted, arr, sizeof(float) * size);

  for (int i = 0; i < size - 1; i++) {
    for (int j = i + 1; j < size; j++) {
      if (sorted[j] < sorted[i]) {
        float temp = sorted[i];
        sorted[i] = sorted[j];
        sorted[j] = temp;
      }
    }
  }

  if (size % 2 == 0) {
    return (sorted[size/2 - 1] + sorted[size/2]) / 2.0;
  } else {
    return sorted[size/2];
  }
}

void loop() {
  float dist_raw = readDistance();

  samples[sampleIndex] = dist_raw;
  sampleIndex = (sampleIndex + 1) % N;
  if (sampleIndex == 0) bufferFilled = true;

  int sampleCount = bufferFilled ? N : sampleIndex;
  float dist_median = getMedian(samples, sampleCount);

  // 시리얼 플로터 출력
  Serial.print("Min:"); Serial.print(_DIST_MIN);
  Serial.print(",raw:"); Serial.print(dist_raw);
  Serial.print(",ema:"); Serial.print(0); // EMA 사용 안함
  Serial.print(",median:"); Serial.print(dist_median);
  Serial.print(",Max:"); Serial.print(_DIST_MAX);
  Serial.println("");

  delay(INTERVAL);
}
