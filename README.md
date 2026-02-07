#include <SoftwareSerial.h>
#include <DFRobotDFPlayerMini.h>

SoftwareSerial mySerial(10, 11); // RX, TX
DFRobotDFPlayerMini player;

int soilPin = A0;
int relayPin = 7;

int soilValue;

void setup() {
  Serial.begin(9600);
  mySerial.begin(9600);

  pinMode(relayPin, OUTPUT);
  digitalWrite(relayPin, HIGH); // Motor OFF (relay active LOW)

  if (!player.begin(mySerial)) {
    Serial.println("DFPlayer not detected");
    while (true);
  }

  player.volume(25);

  Serial.println("Voice-First Smart Irrigation System Started");
}

void loop() {

  soilValue = analogRead(soilPin);
  Serial.print("Soil Moisture Value: ");
  Serial.println(soilValue);

  // DRY SOIL
  if (soilValue < 300) {
    Serial.println("Decision: Start irrigation for 20 minutes");

    player.play(1);        // Voice message
    digitalWrite(relayPin, LOW);  // Motor ON

    delay(20000); // Demo purpose (20 sec instead of 20 min)

    digitalWrite(relayPin, HIGH); // Motor OFF
  }

  // NORMAL SOIL
  else if (soilValue <= 700) {
    Serial.println("Decision: No irrigation required");

    digitalWrite(relayPin, HIGH); // Motor OFF
    player.play(2);
  }

  // WET SOIL
  else {
    Serial.println("Decision: Stop irrigation");

    digitalWrite(relayPin, HIGH); // Motor OFF
    player.play(3);
  }

  Serial.println("----------------------------");
  delay(10000); //prevents repeated alerts
}

