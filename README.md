# Mini-projeto_SEEV
/*Bibliotecas*/
#include <NewPing.h>
#include "Arduino.h"
#include <SPI.h>
#include <Adafruit_GFX.h>
#include <Adafruit_ILI9341.h>

/*Definir os terminais da LDR*/
#define LDR_LED 13
#define LDR 14

/*Definir os terminais dos sensores e buzzer*/
#define TRIG_R 16
#define ECHO_R 17
#define TRIG_C 32
#define ECHO_C 33
#define TRIG_L 25
#define ECHO_L 27
#define BUZZ_LED 12
#define BUZZ 5

NewPing sonarR(16,17);
NewPing sonarC(32,33);
NewPing sonarL(25,27);

/*Definir os terminais do display*/
#define TFT_CS   15
#define TFT_DC   26
#define TFT_MOSI 23
#define TFT_MISO 19
#define TFT_SCLK 18
#define TFT_RST -1 //ligar ao 3V3

/*Variaveis Globais*/
//LDR
int valorLDR;  //armazena o valor do sinal analógico enviado pelo LDR
int luminosidade;  //armazena o valor do sinal analógico convertido em PWM

//Sensores
unsigned long ant = 0;
unsigned int freq = 1000; //Intervalo inicial a 1 seg
unsigned int dist;
unsigned int distD;
unsigned int distC;
unsigned int distE;

/*Criar um objeto tft com indicação dos terminais CS e DC*/
Adafruit_ILI9341 tft = Adafruit_ILI9341(TFT_CS, TFT_DC, TFT_MOSI, TFT_SCLK, TFT_RST, TFT_MISO);

void setup() {
	tft.begin(); //iniciar o display tft
	tft.fillScreen(ILI9341_BLACK); //fundo preto
	tft.setRotation(2); //orientaçao da escrita

	//Caixa de apresentaçao:
	tft.setCursor(20, 10);
	tft.setTextColor(ILI9341_WHITE); //definir a cor do texto em branco
	tft.setTextSize(1); //definir o tamanho do texto
	tft.println("Miniprojeto SEEV 23/24 ");
	tft.setCursor(12, 20);
	tft.println("Diogo Lavos e Pedro Costa");
	tft.drawRect(5, 5, 162, 30, ILI9341_VERDE);//desenhar um retangulo em volta do texto
	tft.drawRect(6, 6, 160, 28, ILI9341_VERDE);

	//Caixa com valores de temperatura e luminusidade:
	tft.setTextColor(ILI9341_BLACK); //definir a cor do texto em preto
	tft.fillRoundRect(172, 5, 146, 30, 10, ILI9341_VERDE); //desenhar um retangulo preenchido e com cantos redondos
	tft.setCursor(176, 10);
	tft.println("TEMPERATURA: ");
	tft.setCursor(270, 10);
	tft.println("C");
	tft.setCursor(176, 22);
	tft.println("LUMINUSIDADE: ");
	tft.setCursor(270, 22);
	tft.println("%");

	//Desenhar três retangulos para representar as barras verticais dos sensores:
	tft.drawRect(60, 50, 50, 124, ILI9341_WHITE);
	tft.drawRect(135, 50, 50, 124, ILI9341_WHITE);
	tft.drawRect(210, 50, 50, 124, ILI9341_WHITE);

	//Definir os pinos como entradas ou saídas:
	pinMode(LDR, INPUT);
	pinMode(LDR_LED, OUTPUT);
	pinMode(BUZZ, OUTPUT);
	pinMode(BUZZ_LED, OUTPUT);

	Serial.begin(115200);
	}

	void loop(){

	  unsigned long at = millis(); //retorna o tempo decorrido em milissegundos desde que o programa Arduino começou a ser executado

	  if(at - ant >= freq){
	    ant=at;

	    //Cálculo das distancias dos sensores:
	    distD = sonarR.ping_cm();
	    distC = sonarC.ping_cm();
	    distE = sonarL.ping_cm();

	    dist = min(min(distD, distC), distE); //verifica qual a menor distancia atraves da funçao min

	    Serial.print(dist); //imprime na consola o valor de distancia
	    Serial.println(" cm");

	    //Preenchimento da coluna esquerda:
	    if (distE > 40) {
	    	tft.fillRect(62, 52, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(62, 72, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(62, 92, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(62, 112, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(62, 132, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(62, 152, 46, 20, ILI9341_BLACK);
  	    } else if (35 < distE && distE <= 40) {
  				tft.fillRect(62, 52, 46, 20, ILI9341_BLACK);
  				tft.fillRect(62, 72, 46, 20, ILI9341_BLACK);
  				tft.fillRect(62, 92, 46, 20, ILI9341_BLACK);
  				tft.fillRect(62, 112, 46, 20, ILI9341_BLACK);
  				tft.fillRect(62, 132, 46, 20, ILI9341_BLACK);
  				tft.fillRect(62, 152, 46, 20, ILI9341_VERDE);
  			  } else if (30 < distE && distE <= 35) {
  				  tft.fillRect(62, 52, 46, 20, ILI9341_BLACK);
  					tft.fillRect(62, 72, 46, 20, ILI9341_BLACK);
  					tft.fillRect(62, 92, 46, 20, ILI9341_BLACK);
  					tft.fillRect(62, 112, 46, 20, ILI9341_BLACK);
  					tft.fillRect(62, 132, 46, 20, ILI9341_VERDE);
  					tft.fillRect(62, 152, 46, 20, ILI9341_VERDE);
  				  } else if (25 < distE && distE <= 30 ) {
  						tft.fillRect(62, 52, 46, 20, ILI9341_BLACK);
  						tft.fillRect(62, 72, 46, 20, ILI9341_BLACK);
  						tft.fillRect(62, 92, 46, 20, ILI9341_BLACK);
  						tft.fillRect(62, 112, 46, 20, ILI9341_LARANJA);
  						tft.fillRect(62, 132, 46, 20, ILI9341_VERDE);
  						tft.fillRect(62, 152, 46, 20, ILI9341_VERDE);
  	    	    } else if (20 < distE && distE <= 25) {
  							tft.fillRect(62, 52, 46, 20, ILI9341_BLACK);
  							tft.fillRect(62, 72, 46, 20, ILI9341_BLACK);
  							tft.fillRect(62, 92, 46, 20, ILI9341_LARANJA);
  							tft.fillRect(62, 112, 46, 20, ILI9341_LARANJA);
  							tft.fillRect(62, 132, 46, 20, ILI9341_VERDE);
  							tft.fillRect(62, 152, 46, 20, ILI9341_VERDE);
  						  } else if (15 < distE && distE <= 20 ) {
  								tft.fillRect(62, 52, 46, 20, ILI9341_BLACK);
  								tft.fillRect(62, 72, 46, 20, ILI9341_VERMELHO);
  								tft.fillRect(62, 92, 46, 20, ILI9341_LARANJA);
  								tft.fillRect(62, 112, 46, 20, ILI9341_LARANJA);
  								tft.fillRect(62, 132, 46, 20, ILI9341_VERDE);
  								tft.fillRect(62, 152, 46, 20, ILI9341_VERDE);
  						    } else if (distE <= 15 ) {
  	  							tft.fillRect(62, 52, 46, 20, ILI9341_VERMELHO);
  	  							tft.fillRect(62, 72, 46, 20, ILI9341_VERMELHO);
  	  							tft.fillRect(62, 92, 46, 20, ILI9341_LARANJA);
  	  							tft.fillRect(62, 112, 46, 20, ILI9341_LARANJA);
  	  							tft.fillRect(62, 132, 46, 20, ILI9341_VERDE);
  	  							tft.fillRect(62, 152, 46, 20, ILI9341_VERDE);
  	  					    }

	    //Preenchimento da coluna centro:
	    if (distC > 40) {
	    	tft.fillRect(137, 52, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(137, 72, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(137, 92, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(137, 112, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(137, 132, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(137, 152, 46, 20, ILI9341_BLACK);
	    	} else if (35 < distC && distC <= 40) {
	    	    tft.fillRect(137, 52, 46, 20, ILI9341_BLACK);
	    	  	tft.fillRect(137, 72, 46, 20, ILI9341_BLACK);
	    	  	tft.fillRect(137, 92, 46, 20, ILI9341_BLACK);
	    	  	tft.fillRect(137, 112, 46, 20, ILI9341_BLACK);
	    	  	tft.fillRect(137, 132, 46, 20, ILI9341_BLACK);
	    	  	tft.fillRect(137, 152, 46, 20, ILI9341_VERDE);
  	    		} else if (30 < distC && distC <= 35) {
  	    			tft.fillRect(137, 52, 46, 20, ILI9341_BLACK);
  	    			tft.fillRect(137, 72, 46, 20, ILI9341_BLACK);
  	    			tft.fillRect(137, 92, 46, 20, ILI9341_BLACK);
  	    			tft.fillRect(137, 112, 46, 20, ILI9341_BLACK);
  	  				tft.fillRect(137, 132, 46, 20, ILI9341_VERDE);
  	  				tft.fillRect(137, 152, 46, 20, ILI9341_VERDE);
      				} else if (25 < distC && distC <= 30 ) {
	    					tft.fillRect(137, 52, 46, 20, ILI9341_BLACK);
  	   					tft.fillRect(137, 72, 46, 20, ILI9341_BLACK);
      					tft.fillRect(137, 92, 46, 20, ILI9341_BLACK);
	    					tft.fillRect(137, 112, 46, 20, ILI9341_LARANJA);
  	    				tft.fillRect(137, 132, 46, 20, ILI9341_VERDE);
  	    				tft.fillRect(137, 152, 46, 20, ILI9341_VERDE);
  	        	  } else if (20 < distC && distC <= 25) {
  	  	    	    tft.fillRect(137, 52, 46, 20, ILI9341_BLACK);
  	  	    	    tft.fillRect(137, 72, 46, 20, ILI9341_BLACK);
      	    	    tft.fillRect(137, 92, 46, 20, ILI9341_LARANJA);
	    	    	    tft.fillRect(137, 112, 46, 20, ILI9341_LARANJA);
  	    	   	  	tft.fillRect(137, 132, 46, 20, ILI9341_VERDE);
  	        	  	tft.fillRect(137, 152, 46, 20, ILI9341_VERDE);
  	  						} else if (15 < distC && distC <= 20 ) {
  	  							tft.fillRect(137, 52, 46, 20, ILI9341_BLACK);
      							tft.fillRect(137, 72, 46, 20, ILI9341_VERMELHO);
  	    						tft.fillRect(137, 92, 46, 20, ILI9341_LARANJA);
  	    						tft.fillRect(137, 112, 46, 20, ILI9341_LARANJA);
  	    						tft.fillRect(137, 132, 46, 20, ILI9341_VERDE);
  	    						tft.fillRect(137, 152, 46, 20, ILI9341_VERDE);
    								} else if (distC <= 15 ) {
    									tft.fillRect(137, 52, 46, 20, ILI9341_VERMELHO);
    									tft.fillRect(137, 72, 46, 20, ILI9341_VERMELHO);
    									tft.fillRect(137, 92, 46, 20, ILI9341_LARANJA);
  										tft.fillRect(137, 112, 46, 20, ILI9341_LARANJA);
    									tft.fillRect(137, 132, 46, 20, ILI9341_VERDE);
    									tft.fillRect(137, 152, 46, 20, ILI9341_VERDE);
    								  }

	    //Preenchimento da coluna direita:
	    if (distD > 40) {
	      tft.fillRect(212, 52, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(212, 72, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(212, 92, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(212, 112, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(212, 132, 46, 20, ILI9341_BLACK);
	    	tft.fillRect(212, 152, 46, 20, ILI9341_BLACK);
	    	    } else if (35 < distD && distD <= 40) {
	    	    	tft.fillRect(212, 52, 46, 20, ILI9341_BLACK);
	    	    	tft.fillRect(212, 72, 46, 20, ILI9341_BLACK);
	    	    	tft.fillRect(212, 92, 46, 20, ILI9341_BLACK);
	    	    	tft.fillRect(212, 112, 46, 20, ILI9341_BLACK);
	    	    	tft.fillRect(212, 132, 46, 20, ILI9341_BLACK);
	    	    	tft.fillRect(212, 152, 46, 20, ILI9341_VERDE);
	    			} else if (30 < distD && distD <= 35) {
	    				tft.fillRect(212, 52, 46, 20, ILI9341_BLACK);
	    				tft.fillRect(212, 72, 46, 20, ILI9341_BLACK);
	    				tft.fillRect(212, 92, 46, 20, ILI9341_BLACK);
	    				tft.fillRect(212, 112, 46, 20, ILI9341_BLACK);
	    				tft.fillRect(212, 132, 46, 20, ILI9341_VERDE);
	    				tft.fillRect(212, 152, 46, 20, ILI9341_VERDE);
	    				} else if (25 < distD && distD <= 30 ) {
	    					tft.fillRect(212, 52, 46, 20, ILI9341_BLACK);
	    					tft.fillRect(212, 72, 46, 20, ILI9341_BLACK);
	    					tft.fillRect(212, 92, 46, 20, ILI9341_BLACK);
	    					tft.fillRect(212, 112, 46, 20, ILI9341_LARANJA);
	    					tft.fillRect(212, 132, 46, 20, ILI9341_VERDE);
	    					tft.fillRect(212, 152, 46, 20, ILI9341_VERDE);
	    	    	    	} else if (20 < distD && distD <= 25) {
	    	    	    		tft.fillRect(212, 52, 46, 20, ILI9341_BLACK);
	    	    	    		tft.fillRect(212, 72, 46, 20, ILI9341_BLACK);
	    	    	    		tft.fillRect(212, 92, 46, 20, ILI9341_LARANJA);
	    	    	    		tft.fillRect(212, 112, 46, 20, ILI9341_LARANJA);
	    	    	    		tft.fillRect(212, 132, 46, 20, ILI9341_VERDE);
	    	    	    		tft.fillRect(212, 152, 46, 20, ILI9341_VERDE);
	    						} else if (15 < distD && distD <= 20 ) {
	    							tft.fillRect(212, 52, 46, 20, ILI9341_BLACK);
	    							tft.fillRect(212, 72, 46, 20, ILI9341_VERMELHO);
	    							tft.fillRect(212, 92, 46, 20, ILI9341_LARANJA);
	    							tft.fillRect(212, 112, 46, 20, ILI9341_LARANJA);
	    							tft.fillRect(212, 132, 46, 20, ILI9341_VERDE);
	    							tft.fillRect(212, 152, 46, 20, ILI9341_VERDE);
									} else if (distD <= 15 ) {
										tft.fillRect(212, 52, 46, 20, ILI9341_VERMELHO);
										tft.fillRect(212, 72, 46, 20, ILI9341_VERMELHO);
										tft.fillRect(212, 92, 46, 20, ILI9341_LARANJA);
										tft.fillRect(212, 112, 46, 20, ILI9341_LARANJA);
										tft.fillRect(212, 132, 46, 20, ILI9341_VERDE);
										tft.fillRect(212, 152, 46, 20, ILI9341_VERDE);
									}

	    //Controlo do buzzer e do LED vermelho consoante a distancia:

	    tone (BUZZ, 500); //liga o buzzer com frequencia de 500hz
	    digitalWrite(BUZZ_LED, HIGH); //liga o led vermelho

	    if (dist>40){
			noTone(BUZZ); //desliga o buzzer
			digitalWrite(BUZZ_LED, LOW); //desliga o led
			} else if (35 < dist && dist <= 40) {
				delay(250);
				noTone(BUZZ);
				digitalWrite(BUZZ_LED, LOW);
				} else if (30 < dist && dist <= 35) {
					delay(200);
					noTone(BUZZ);
					digitalWrite(BUZZ_LED, LOW);
					} else if (25 < dist && dist <= 30) {
						delay(150);
						noTone(BUZZ);
						digitalWrite(BUZZ_LED, LOW);
						} else if (20 < dist && dist <= 25) {
							delay(100);
							noTone(BUZZ);
							digitalWrite(BUZZ_LED, LOW);
							} else if (15 < dist && dist <= 20) {
								delay(50);
								noTone(BUZZ);
								digitalWrite(BUZZ_LED, LOW);
								} else if (dist <= 15){

								}
	    // Calcular duração intervalo entre apitos com base na distância
	    freq = map(dist, 0, 200, 70, 1500);
	}
	  //Controlo da LDR:
	  valorLDR = analogRead(LDR); //faz a leitura da LDR e guarda o valor analógico
	  luminosidade = map(valorLDR, 0, 4100, 0, 100); //Mapear o valor lido para a escala de 0 a 100%
	  tft.fillRect(255, 22, 12, 11, ILI9341_VERDE);
	  tft.setCursor(255, 22);
	  tft.println(luminosidade);
	  analogWrite(13, luminosidade); //muda a luminusidade do led de acordo com o valor de luminosidade

	}
