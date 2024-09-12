#include <LiquidCrystal.h>

#define PIN_TRIG 5
#define PIN_ECHO 3

float distance = 0;

#define buttonPin 6

int buzzer = 13;

int bMais = 2;
int bMenos = 4;
int led = 13;

bool relogioPausado = false;
bool onAlarmScreen = false;
bool alarmState = false;

int press = 0;

int hora = 0;
int minuto = 0;
int segundo = 0;

int alarmHour = 0;
int alarmMinute = 0;

unsigned long ultimoTempoAtualizacao = 0;
const int intervaloAtualizacao = 1000;

LiquidCrystal lcd(12, 11, 10, 9, 8, 7);

unsigned long previousMillis = 0;
const unsigned long interval = 50; // Intervalo entre medições do sensor ultrasonico

void setup() {
  pinMode(buttonPin, INPUT);
  pinMode(bMais, INPUT_PULLUP);
  pinMode(bMenos, INPUT_PULLUP);
  pinMode(buzzer, OUTPUT);
  pinMode(PIN_TRIG, OUTPUT);
  pinMode(PIN_ECHO, INPUT);

  Serial.begin(9600);


  clockScreen();
  muteUnmute();
}

void loop() {

  if(!onAlarmScreen){ // "Not" alarm Screen, so it's in "time" screen
    int b = checkButton(buttonPin);
    if (b == 0 && (!relogioPausado)) atualizarRelogio(); 
    if (b == 1 && !relogioPausado) alarmScreen(); 
    if (b == 3) {
      holdEvent(); // the clock stops and open the Time Edit
    }

  int b1 = digitalRead(bMais); // Lê o estado do botão "mais"
  

  if (b1 == LOW && press == 0) {
    press = 1;
    muteUnmute(); 
  }
  if(b1 == HIGH){
    press = 0;
  }

  }
  if(onAlarmScreen){
    int b = checkButton(buttonPin);
    if(b == 0) atualizarRelogio();
    if(b == 1){
      clockScreen();
      onAlarmScreen = !onAlarmScreen;
    }
    if(b == 3){
      editTimeAlarm(); // the clock keeps running and open the Alarm Edit 
    }

    int b1 = digitalRead(bMais);
  

  if (b1 == LOW && press == 0) {
    press = 1;
    muteUnmute(); 
  }
  if(b1 == HIGH){
    press = 0;
  }
  }
} 


void holdEvent() {
   relogioPausado = !relogioPausado;

   if(relogioPausado){
    editTimeClock();
   }
}

int modoEdicao = 0;  // 0 para horas, 1 para minutos, 2 para segundos

void editTimeClock() {
  modoEdicao = 0;

  while (relogioPausado) {
    int estadoBotaoMais = digitalRead(bMais);
    int estadoBotaoMenos = digitalRead(bMenos);

    if (estadoBotaoMais == LOW) {  // Botão "mais" pressionado (LOW por causa do INPUT_PULLUP)
      if (modoEdicao == 0) hora = (hora + 1) % 24;
      else if (modoEdicao == 1) minuto = (minuto + 1) % 60;
      else if (modoEdicao == 2) segundo = (segundo + 1) % 60;
      atualizarDisplay();
      delay(100);  // Pequeno debounce
    }

    if (estadoBotaoMenos == LOW) {  // Botão "menos" pressionado (LOW por causa do INPUT_PULLUP)
      if (modoEdicao == 0) hora = (hora == 0) ? 23 : hora - 1;
      else if (modoEdicao == 1) minuto = (minuto == 0) ? 59 : minuto - 1;
      else if (modoEdicao == 2) segundo = (segundo == 0) ? 59 : segundo - 1;
      atualizarDisplay();
      delay(100);  // Pequeno debounce
    }

    // Botão principal (buttonPin) para alternar entre horas, minutos e segundos
    int b = checkButton(buttonPin);
    if (b == 1) {  // Pressionado para alternar entre horas, minutos e segundos
      modoEdicao = (modoEdicao + 1);
      delay(100);  // Debounce simples
    }
    if(modoEdicao > 1){
      segundo = -1;
      relogioPausado = !relogioPausado;
      return; // Um "return" só pra ter certeza que vai sair da função 
    }
  }
}

void editTimeAlarm() {
  modoEdicao = 0;

  while (true) {
    int estadoBotaoMais = digitalRead(bMais);
    int estadoBotaoMenos = digitalRead(bMenos);

    if (estadoBotaoMais == LOW) {  // Botão "mais" pressionado
      if (modoEdicao == 0) alarmHour = (alarmHour + 1) % 24;
      else if (modoEdicao == 1) alarmMinute = (alarmMinute + 1) % 60;
      atualizarAlarmeDisplay();
      delay(200);
    }

    if (estadoBotaoMenos == LOW) {  // Botão "menos" pressionado
      if (modoEdicao == 0) alarmHour = (alarmHour == 0) ? 23 : alarmHour - 1;
      else if (modoEdicao == 1) alarmMinute = (alarmMinute == 0) ? 59 : alarmMinute - 1;
      atualizarAlarmeDisplay();
      delay(200);
    }

    // Alterna entre edição de horas e minutos do alarme
    int b = checkButton(buttonPin);
    if (b == 1) {
      modoEdicao = (modoEdicao + 1);
      delay(200);
    }

    // Sai do modo de edição ao final
    if (modoEdicao > 1) {
      return;
    }
  }
}

// Button timing variables
int debounce = 20;          // ms debounce period to prevent flickering when pressing or releasing the button
int DCgap = 250;            // max ms between clicks for a double click event
int holdTime = 2000;        // ms hold period: how long to wait for press+hold event
int longHoldTime = 3000;    // ms long hold period: how long to wait for press+hold event

// Button variables
boolean buttonVal = HIGH;   // value read from button
boolean buttonLast = HIGH;  // buffered value of the button's previous state
boolean DCwaiting = false;  // whether we're waiting for a double click (down)
boolean DConUp = false;     // whether to register a double click on next release, or whether to wait and click
boolean singleOK = true;    // whether it's OK to do a single click
long downTime = -1;         // time the button was pressed down
long upTime = -1;           // time the button was released
boolean ignoreUp = false;   // whether to ignore the button release because the click+hold was triggered
boolean waitForUp = false;        // when held, whether to wait for the up event
boolean holdEventPast = false;    // whether or not the hold event happened already
boolean longHoldEventPast = false;// whether or not the long hold event happened already


int checkButton(int ButtonPin) {    
   int event = 0;
   buttonVal = digitalRead(ButtonPin);
   // Button pressed down
   if (buttonVal == LOW && buttonLast == HIGH && (millis() - upTime) > debounce)
   {
       downTime = millis();
       ignoreUp = false;
       waitForUp = false;
       singleOK = true;
       holdEventPast = false;
       longHoldEventPast = false;
       if ((millis()-upTime) < DCgap && DConUp == false && DCwaiting == true)  DConUp = true;
       else  DConUp = false;
       DCwaiting = false;
   }
   // Button released
   else if (buttonVal == HIGH && buttonLast == LOW && (millis() - downTime) > debounce)
   {        
       if (not ignoreUp)
       {
           upTime = millis();
           if (DConUp == false) DCwaiting = true;
           else
           {
               event = 2;
               DConUp = false;
               DCwaiting = false;
               singleOK = false;
           }
       }
   }
   // Test for normal click event: DCgap expired
   if ( buttonVal == HIGH && (millis()-upTime) >= DCgap && DCwaiting == true && DConUp == false && singleOK == true && event != 2)
   {
       event = 1;
       DCwaiting = false;
   }
   // Test for hold
   if (buttonVal == LOW && (millis() - downTime) >= holdTime) {
       // Trigger "normal" hold
       if (not holdEventPast)
       {
           event = 3;
           waitForUp = true;
           ignoreUp = true;
           DConUp = false;
           DCwaiting = false;
           //downTime = millis();
           holdEventPast = true;
       }
       // Trigger "long" hold
       if ((millis() - downTime) >= longHoldTime)
       {
           if (not longHoldEventPast)
           {
               event = 4;
               longHoldEventPast = true;
           }
       }
   }
   buttonLast = buttonVal;
   return event;
}

void clockScreen(){
  lcd.clear();
  
  lcd.begin(16, 2);
  lcd.print("Hora ");
  lcd.setCursor(7, 0);
  lcd.print(":");
  lcd.setCursor(10, 0);
  lcd.print(":");
  atualizarDisplay();
  desenharA_PorraDoSino();
}

void alarmScreen(){
  lcd.clear(); //clear the whole display to blank 
  onAlarmScreen = !onAlarmScreen; //set alarmScreen to True
  desenharA_PorraDoSino();

  //print the alarm time 
  lcd.setCursor(5,0);
  lcd.print("Alarme  ");

  lcd.setCursor(6,1);
  lcd.print(alarmHour < 10 ? "0" : "");  // Adiciona 0 à esquerda se for menor que 10
  lcd.print(alarmHour);

  lcd.setCursor(8,1);
  lcd.print(":");

  lcd.setCursor(9,1);
  lcd.print(alarmMinute < 10 ? "0" : "");  // Adiciona 0 à esquerda se for menor que 10
  lcd.print(alarmMinute);

}

void atualizarDisplay() {
  lcd.setCursor(5, 0);
  lcd.print(hora < 10 ? "0" : "");  // Adiciona 0 à esquerda se for menor que 10
  lcd.print(hora);

  lcd.setCursor(8, 0);
  lcd.print(minuto < 10 ? "0" : "");  // Adiciona 0 à esquerda se for menor que 10
  lcd.print(minuto);

  lcd.setCursor(11, 0);
  lcd.print(segundo < 10 ? "0" : "");  // Adiciona 0 à esquerda se for menor que 10
  lcd.print(segundo);
}

void atualizarAlarmeDisplay() {
  lcd.setCursor(6, 1);
  lcd.print(alarmHour < 10 ? "0" : "");  
  lcd.print(alarmHour);

  lcd.setCursor(8, 1);
  lcd.print(":");

  lcd.setCursor(9, 1);
  lcd.print(alarmMinute < 10 ? "0" : "");  
  lcd.print(alarmMinute);
}

void atualizarRelogio() {
  if (millis() - ultimoTempoAtualizacao >= intervaloAtualizacao) {
    ultimoTempoAtualizacao = millis();

    segundo++;

    if (segundo > 59) {
      segundo = 0;
      minuto++;
      checkRingAlarm();
    }

    if (minuto > 59) {
      minuto = 0;
      hora++;
    }

    if (hora > 23) {
      hora = 0;
      delay(1728);
    }

    if(!onAlarmScreen) atualizarDisplay();
  }
}

void desenharSino() {
  // Definindo o bitmap para o ícone do sino em 5x8 pixels
  byte sino[8] = {
    0b00100,  //   *
    0b00100,  //   *
    0b00100,  //   *
    0b01110,  //  ***
    0b01110,  //  ***
    0b11111,  // *****
    0b00100,  //   *
    0b00000   // (vazio na parte inferior)
  };

  // Cria o caractere personalizado no índice 0
  lcd.createChar(0, sino);

  // Posiciona o cursor no canto superior direito (coluna 15, linha 0)
  lcd.setCursor(15, 0);

  // Exibe o caractere do sino no display
  lcd.write(byte(0));
}


void desenharSinoMudo() {
  // Definindo o bitmap para o ícone de sino com travessão diagonal em 5x8 pixels
  byte sinoComTravessaoDiagonal[8] = {
    0b00100,  //   *  
    0b00100,  //   *  
    0b00101,  //   * *
    0b01110,  //  *** 
    0b01110,  //  *** 
    0b11011,  // **  *
    0b10100,  // *  * 
    0b00000   // (vazio na parte inferior)
  };

  // Cria o caractere personalizado no índice 0
  lcd.createChar(0, sinoComTravessaoDiagonal);

  // Posiciona o cursor no canto superior direito (coluna 15, linha 0)
  lcd.setCursor(15, 0);

  // Exibe o caractere do sino com travessão diagonal no display
  lcd.write(byte(0));
}

void muteUnmute(){
  alarmState = !alarmState;
  if(alarmState == true){
    desenharSino();
  }
  if(alarmState == false){
    desenharSinoMudo();
  }
}

void desenharA_PorraDoSino(){
  if(alarmState == true){
    desenharSino();
  }
  if(alarmState == false){
    desenharSinoMudo();
  }
}

void ultrasonicValue(){
    unsigned long currentMillis = millis();

  // Verifica se é hora de fazer uma nova medição
  if (currentMillis - previousMillis >= interval) {
    previousMillis = currentMillis; // Atualiza o tempo da última medição

    // Inicia uma nova medição
    digitalWrite(PIN_TRIG, LOW);
    delayMicroseconds(2); // Tempo para garantir que o sinal está baixo

    // Gera o pulso de trigger
    digitalWrite(PIN_TRIG, HIGH);
    unsigned long pulseStart = micros(); // Tempo de início do pulso
    while (micros() - pulseStart < 10) {
      // Aguarda 10 microssegundos
    }
    digitalWrite(PIN_TRIG, LOW);

    // Leia o resultado
    int duration = pulseIn(PIN_ECHO, HIGH);
    distance = duration / 58.0; // Converte a duração para distância em cm
    distance;
  }
}

void playBuzzer(){
  tone(buzzer, 500);
}

void checkRingAlarm(){
  int in = 0;
  if(hora == alarmHour && minuto == alarmMinute){
    while(in == 0){
      if(distance > 20){
      playBuzzer();
      atualizarRelogio();
      }else{
        in == 1;
      }

    }
    noTone(buzzer);
    in = 1;
    return;
  }
}
