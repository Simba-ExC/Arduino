## 

 ```c++
const int button = 2; 

int lastButton = LOW; //предыдущее состояние кнопки
int curButton = LOW; //Текущее состояние кнопки

int latchPin = 8;
int clockPin = 12;
int dataPin = 11;

int numOfRegisters = 2;
byte* registerState;

int speed = 1000;


void setup() 
{
  Serial.begin(9600);

  pinMode(2, INPUT_PULLUP);
  attachInterrupt(0, Button_P, FALLING);

	registerState = new byte[numOfRegisters];
	for (size_t i = 0; i < numOfRegisters; i++) 
  {
		registerState[i] = 0;
	}
	pinMode(latchPin, OUTPUT);
	pinMode(clockPin, OUTPUT);
	pinMode(dataPin, OUTPUT);
}

int debounce(int last, int button )
{
  int current = digitalRead(button);
  if(last != current) //если состояние изменилось
  {
    delay(5);
    current = digitalRead(button);
  }
return current;
}

void Button_P() 
{
curButton = debounce(lastButton,  button);
if(lastButton == HIGH && curButton == LOW)
{
  switch(speed)
  {
    case 1000: speed = 750; Serial.print("Скорость = "); Serial.println(speed);break;
    case 750: speed = 500;Serial.print("Скорость = "); Serial.println(speed);break;
    case 500: speed = 250;Serial.print("Скорость = "); Serial.println(speed);break;
    case 250: speed = 100;Serial.print("Скорость = "); Serial.println(speed);break;
    case 100: speed = 50;Serial.print("Скорость = "); Serial.println(speed);break;
    case 50: speed = 10;Serial.print("Скорость = "); Serial.println(speed);break;
    case 10: speed = 1000;Serial.print("Скорость = "); Serial.println(speed);break;  
  }
}

lastButton = curButton;
}

void loop() 
{
		for (int k = 0; k < 16; k++)
    {
			regWrite(k, HIGH);
			delay(speed);
			regWrite(k, LOW);
		}
}

void regWrite(int pin, bool state)
{
int reg = pin / 8;
int actualPin = pin - (8 * reg);
digitalWrite(latchPin, LOW);
for (int i = 0; i < numOfRegisters; i++)
{
	byte* states = &registerState[i];
	if (i == reg)
  {
		bitWrite(*states, actualPin, state);
	}
shiftOut(dataPin, clockPin, MSBFIRST, *states);
}
digitalWrite(latchPin, HIGH);
}


```

![Alt Text](./Task9.1.gif)