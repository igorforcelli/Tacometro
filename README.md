DESENVOLVIMENTO DE UM TACÔMETRO DE CUSTO ACESSÍVEL MICROCONTROLADO

O presente trabalho consiste no desenvolvimento de tacômetro de baixo custo utilizando a ESP32 (ou Arduino). A proposta consiste em construir um tacômetro sem contato utilizando o modulo HW-201 e desenvolver um código para o Arduino transformar os pulsos recebidos do módulo em rotação por minuto (RPM).

#include <Wire.h>               // Biblioteca para comunicação i2C
#include <LiquidCrystal_I2C.h>  // Biblioteca display LCD 16x2 i2C

LiquidCrystal_I2C lcd(0x3F, 16, 2);  //ENDEREÇO DO DiSPLAY


int vcc = 34;
int gnd = 35;
int sinal = 32;

long unsigned int anterior = 0;
int atual = 0;
int rpm = 0;
int var = 0;

int leitura_anterior = 1;
int leitura_atual = 0;
int numero_de_variacoes = 0;

int intervalo = 1;  //intervalo de leitura - a cada 1 segundo é feita uma leitura
int intervalo_2 = 2000000;

long long unsigned marcador_de_tempo = 0;
long long unsigned marcador_de_tempo_2 = 0;
long long unsigned tempo_display = 0;

void setup() {
  pinMode(vcc, OUTPUT);
  digitalWrite(vcc, HIGH);

  pinMode(gnd, OUTPUT);
  digitalWrite(gnd, LOW);

  pinMode(sinal, INPUT);

  marcador_de_tempo = millis();  // Ao inicializar o arduino, marcamos o tempo
  Serial.begin(9600);

  lcd.init();       // Inicia a comunicação com o display LCD
  lcd.clear();      // Limpa tudo o que estiver escrito no display LCD
  lcd.backlight();  // Aciona a luz de fundo do display LCD

  lcd.setCursor(3, 0);     // Posiciona o cursor na segunda coluna(2) e na primeira linha(0) do LCD
  lcd.print("TACOMETRO");  // Escreve na tela a palavra apartir do local onde esta o cursor
  lcd.setCursor(4, 1);

  lcd.print("0 RPM");
}

//-------------- FUNÇÃO TELA -----------------//
void TELA(int RPM) {
  //  lcd.clear();
  //  lcd.setCursor(2, 0); // Posiciona o cursor na segunda coluna(2) e na primeira linha(0) do LCD
  //  lcd.print("TACOMETRO"); // Escreve na tela a palavra apartir do local onde esta o cursor
  lcd.setCursor(4, 1);

  lcd.print(RPM);
  lcd.print(" RPM   ");
}

void loop() {

  // Se houver se passado um tempo maior do que o intervalo, realiza outra leitura
  //if (millis() - marcador_de_tempo >= intervalo) {

    leitura_atual = digitalRead(sinal);

    // Se a leitura atual for diferente da leitura anterior
    if (leitura_anterior != leitura_atual) {
      if (leitura_anterior > leitura_atual) {
        rpm = 60000000 / (micros() - anterior);
        var = millis() - anterior;
        anterior = micros();
        if (micros() - tempo_display >= 1000000) {
          TELA(rpm);
          tempo_display = micros();
          //Serial.print("anterior = ");
         // Serial.println(anterior);

          //Serial.print("variação = ");
          //Serial.println(var);

          //Serial.print("RPM = ");
          //Serial.println(rpm);
        }
      }
      leitura_anterior = leitura_atual;
    }
    marcador_de_tempo = millis();  
  //}
  if (micros() - anterior >= intervalo_2) {
    lcd.clear();      // Limpa tudo o que estiver escrito no display LCD
    lcd.backlight();  // Aciona a luz de fundo do display LCD

    lcd.setCursor(3, 0);     // Posiciona o cursor na segunda coluna(2) e na primeira linha(0) do LCD
    lcd.print("TACOMETRO");  // Escreve na tela a palavra apartir do local onde esta o cursor
    lcd.setCursor(4, 1);

    lcd.print("0 RPM");

    anterior = micros();
  }
}
