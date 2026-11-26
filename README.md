// Cidade Inteligente - Toque de Recolher


const int LED  = 8;    // poste
const int BUZZ = 10;   // buzzer
const int TRIG = 6;    // HC-SR04
const int ECHO = 7;    // HC-SR04
// LDR no A0 | Potenciômetro no A1


bool noite = false;


long distancia() {
  digitalWrite(TRIG, LOW);  delayMicroseconds(2);
  digitalWrite(TRIG, HIGH); delayMicroseconds(10);
  digitalWrite(TRIG, LOW);
  return pulseIn(ECHO, HIGH, 30000) * 0.034 / 2;
}


void setup() {
  pinMode(LED, OUTPUT);
  pinMode(BUZZ, OUTPUT);
  pinMode(TRIG, OUTPUT);
  pinMode(ECHO, INPUT);
  Serial.begin(9600);
}


void loop() {
  int limite = analogRead(A1);   // potenciômetro: define quando é noite
  int luz    = analogRead(A0);   // LDR: mede a luz
  bool escuro = luz < limite;


  // mudou de dia para noite (ou o contrário)
  if (escuro != noite) {
    noite = escuro;
    digitalWrite(LED, noite);                // poste acende à noite
    tone(BUZZ, noite ? 800 : 1500, 1000);    // aviso do toque de recolher
  }


  long d = distancia();


  Serial.print("luz: ");      Serial.print(luz);
  Serial.print("  limite: "); Serial.print(limite);
  Serial.print("  dist: ");   Serial.println(d);


  // alguém tentando fugir pela fronteira à noite
  if (noite && d > 0 && d < 15) {
    tone(BUZZ, 2000, 300);
  }


  delay(200);
}
