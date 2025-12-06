📌 Sistema de Monitoramento de Temperatura e Umidade com ESP32, DHT22 e MQTT

Este projeto apresenta um sistema IoT desenvolvido com ESP32, sensor DHT22, LED indicador (simulando um umidificador) e comunicação MQTT.
O objetivo é monitorar a temperatura e a umidade do ambiente, enviar estes dados para um broker MQTT e acionar automaticamente um umidificador simulado quando a umidade estiver baixa.

🧩 Componentes Utilizados

ESP32 DevKit

Sensor DHT22

LED vermelho (representando o umidificador)

Resistor de 220Ω

Jumpers

Simulador Wokwi

🔧 Funcionamento do Sistema

O ESP32 realiza a leitura da temperatura e umidade através do sensor DHT22.

Os valores são exibidos no Monitor Serial do Wokwi.

Quando a umidade fica abaixo de 40%, o LED acende, representando o umidificador ligado (LED ON).

Quando a umidade está acima ou igual a 40%, o LED desliga, representando o umidificador desligado (LED OFF).

Os dados são publicados automaticamente no broker MQTT nos tópicos:

tatamela/temperatura

tatamela/umidade

tatamela/status

🌐 Conexão MQTT

O projeto utiliza o broker público:

broker.hivemq.com
Porta: 1883 (sem SSL)


Ferramenta utilizada para visualizar e testar as mensagens MQTT:
➡ HiveMQ WebSocket Client
https://www.hivemq.com/demos/websocket-client/

🗃️ Tópicos Publicados
Tópico	Conteúdo Publicado
tatamela/temperatura	Temperatura em °C
tatamela/umidade	Umidade em %
tatamela/status	LED ON (umidificador ligado) ou LED OFF (umidificador desligado)


💻 Código Utilizado
// #include <WiFi.h>
#include <PubSubClient.h>

// Defina o SSID e senha do seu WiFi
const char* ssid = "SEU_SSID";
const char* password = "SUA_SENHA";

// Defina o servidor MQTT (broker)
const char* mqtt_server = "broker.hivemq.com";

// Inicializando as variáveis
WiFiClient espClient;
PubSubClient client(espClient);

void setup() {
  // Inicialize o monitor serial
  Serial.begin(115200);

  // Conecta no WiFi
  setup_wifi();

  // Configura o servidor MQTT
  client.setServer(mqtt_server, 1883);

  // Conecta ao servidor MQTT
  while (!client.connected()) {
    Serial.print("Tentando conectar ao broker MQTT...");
    if (client.connect("ESP32Client")) {
      Serial.println("Conectado!");
      // Enviar uma mensagem para o tópico "teste"
      client.publish("teste", "Olá do Wokwi!");
    } else {
      Serial.print("Falha ao conectar. Código: ");
      Serial.print(client.state());
      delay(5000);
    }
  }
}

void loop() {
  // Mantém a conexão com o broker MQTT
  client.loop();
}

void setup_wifi() {
  // Conecta-se à rede Wi-Fi
  Serial.print("Conectando ao WiFi");
  WiFi.begin(ssid, password);

  while (WiFi.status() != WL_CONNECTED) {
    delay(1000);
    Serial.print(".");
  }
  Serial.println("Conectado ao WiFi!");
}

#include "DHT.h"

#define DHTPIN 15     
#define DHTTYPE DHT22   

#define LEDPIN 2  // LED no pino 2 (GPIO2)

DHT dht(DHTPIN, DHTTYPE);

void setup() {
  Serial.begin(115200);
  pinMode(LEDPIN, OUTPUT);
  dht.begin();
}

void loop() {
  float humidity = dht.readHumidity();
  float temperature = dht.readTemperature();

  if (isnan(humidity) || isnan(temperature)) {
    Serial.println("Falha ao ler o sensor DHT!");
    return;
  }

  Serial.print("Umidade: ");
  Serial.print(humidity);
  Serial.print(" %  |  Temperatura: ");
  Serial.print(temperature);
  Serial.println(" °C");

  if (humidity < 35) {
    digitalWrite(LEDPIN, HIGH);
    Serial.println("Umidade baixa — umidificador ativado (LED ON)");
  } else {
    digitalWrite(LEDPIN, LOW);
    Serial.println("Umidade normal — umidificador desativado (LED OFF)");
  }

  delay(2000);
}


▶️ Como Executar o Projeto

Abrir o projeto no Wokwi:
https://wokwi.com/projects/448631472741586945

Rodar o código.

Abrir o Serial Monitor no Wokwi.

Acessar o HiveMQ WebSocket Client e conectar ao broker.

Assinar os tópicos do projeto.

Acompanhar os dados chegando em tempo real e o acionamento automático do “umidificador”.

📹 Demonstração em Vídeo

(https://youtu.be/P92YAEfHmr0?si=yrCSUXPZgcV3zC4i)

📚 Objetivo Educacional

Este projeto demonstra os conceitos essenciais de Internet das Coisas (IoT), incluindo:

Leitura de sensores ambientais

Acionamento automático de atuadores (umidificador simulado)

Envio de dados para a nuvem via MQTT

Integração com monitoramento em tempo real

Uso de simuladores eletrônicos

✨ Autora

Thais Melo



