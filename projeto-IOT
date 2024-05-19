/*******************************************************************************
* Master Kit - Hello Blynk
* Primeiros Passos com o aplicativo Blynk IoT.
* (Baseado no exemplo padrão do Blynk)
*******************************************************************************/

// Declaracao dos parametros de conexao do aplicativo
// Alterar os codigos abaixo de acordo com o que foi gerado na plataforma
#define BLYNK_TEMPLATE_ID "TMPL2kH9PxQtl"
#define BLYNK_TEMPLATE_NAME "automação e saúde"
#define BLYNK_AUTH_TOKEN "XihUIb6ZBcvnAj6ExIaaUNmmbU0Os-dr"

// Definicao do monitoramento de conexao da placa pela serial
#define BLYNK_PRINT Serial

// Adicao das bibliotecas
#include <ESP8266_Lib.h>
#include <BlynkSimpleShieldEsp8266.h>
#include <SoftwareSerial.h>
#include <DHT.h>

// Declaracao da variavel que armazena o codigo de autenticacao para conexao
char auth[] = BLYNK_AUTH_TOKEN;

// Declaracao do nome e senha da rede Wi-Fi
// Altere as variaveis abaixo com o nome e senha da sua rede Wi-Fi
char ssid[] = "Joy";
char pass[] = "96944604";

// Criacao do objeto serial para comunicacao com o ESP8266
SoftwareSerial EspSerial(10, 11); // RX, TX

// Declaracao da variavel que armazena a velocidade de comunicacao do modulo
const int ESP8266_BAUD = 9600;

// Confiracao do objeto 'wifi' para usar a serial do ESP8266 para conexao
ESP8266 wifi(&EspSerial);

#define DHTPIN 5     // Pino do sensor DHT11
#define DHTTYPE DHT11 // Tipo do sensor DHT11
DHT dht(DHTPIN, DHTTYPE); // Cria um objeto DHT para o sensor

int buzzerPin = 9; // Pino do buzzer
int soundSensorPin = A0; // Pino do sensor de som
int pirSensorPin = 7; // Pino do sensor PIR
bool buzzerState = false; // Estado do buzzer
bool buzzerEnabled = false; // Estado do botão do buzzer
bool pirSensorEnabled = false; // Estado do botão do sensor PIR
int ledActivationCount = 0; // Contador de ativação dos LEDs

// Funcao que le os pinos virtuais V2, V3 e V4 e controla os LEDs correspondentes
BLYNK_WRITE(V2){
  int led1Value = param.asInt(); // Le o valor do pino virtual V2
  digitalWrite(13, led1Value); // Aciona o LED conectado ao pino 13 de acordo com o valor lido
}

BLYNK_WRITE(V3){
  int led2Value = param.asInt(); // Le o valor do pino virtual V3
  digitalWrite(12, led2Value); // Aciona o LED conectado ao pino 12 de acordo com o valor lido
}

BLYNK_WRITE(V4){
  int led3Value = param.asInt(); // Le o valor do pino virtual V4
  digitalWrite(8, led3Value); // Aciona o LED conectado ao pino 8 de acordo com o valor lido
}

BLYNK_WRITE(V8) { // Controle do buzzer pelo botao no app
  buzzerEnabled = param.asInt() == 1;
  if (!buzzerEnabled) {
    buzzerState = false; // Define o estado do buzzer como não tocando
  }
}

BLYNK_WRITE(V9) { // Controle do sensor PIR pelo botao no app
  pirSensorEnabled = param.asInt() == 1;
}

// Configuracao do codigo
void setup(){

  // Inicializacao do monitor serial
  Serial.begin(9600);

  // Configura os pinos dos LEDs como saida
  pinMode(13, OUTPUT);
  pinMode(12, OUTPUT);
  pinMode(8, OUTPUT);

  // Configura o pino do buzzer como saida
  pinMode(buzzerPin, OUTPUT);

  // Configura o pino do sensor PIR como entrada
  pinMode(pirSensorPin, INPUT);

  // Inicializa a comunicacao serial do ESP8266
  EspSerial.begin(ESP8266_BAUD);
  delay(10);

  // Inicializa o sensor DHT11
  dht.begin();

  // Inicializacao da comunicacao e conexao do modulo ao aplicativo
  Blynk.begin(auth, wifi, ssid, pass);

}

// Repeticao do codigo
void loop(){
  Blynk.run(); // Mantem a conexao ativa com o aplicativo e processa comandos recebidos ou enviados

  // Leitura dos dados do sensor DHT11
  float temperatura = dht.readTemperature(); // Lê a temperatura
  float umidade = dht.readHumidity();       // Lê a umidade

  if (!isnan(temperatura) && !isnan(umidade)) {
    // Envia dados para o aplicativo Blynk
    Blynk.virtualWrite(V5, temperatura); // Atualiza o widget de temperatura (V5)
    Blynk.virtualWrite(V6, umidade);     // Atualiza o widget de umidade (V6)
  }

  // Leitura do sensor de som a cada 5 segundos
  static unsigned long lastSoundTime = 0;
  unsigned long currentMillis = millis();
  if (currentMillis - lastSoundTime >= 5000) {
    int soundValue = analogRead(soundSensorPin);
    Blynk.virtualWrite(V7, soundValue);
    lastSoundTime = currentMillis;
  }

  // Controle dos LEDs pelo sensor PIR
  if (pirSensorEnabled) {
    int pirValue = digitalRead(pirSensorPin);
    if (pirValue == HIGH) {
      digitalWrite(13, HIGH);
      digitalWrite(12, HIGH);
      digitalWrite(8, HIGH);
      delay(1000); // Mantém os LEDs ligados por 1 segundo
      digitalWrite(13, LOW);
      digitalWrite(12, LOW);
      digitalWrite(8, LOW);
      
      ledActivationCount++; // Incrementa o contador de ativação dos LEDs

      // Se os LEDs forem acesos 4 vezes e o buzzer estiver habilitado, toca o buzzer 2 vezes
      if (ledActivationCount >= 4 && buzzerEnabled) {
        for (int i = 0; i < 2; i++) {
          tone(buzzerPin, 1000); // Toca o buzzer
          delay(500);
          noTone(buzzerPin); // Desliga o buzzer
          delay(500);
        }
        ledActivationCount = 0; // Reseta o contador de ativação dos LEDs
      }
    }
  }

  delay(100); // Espera 100 milissegundos antes de ler novamente
}
