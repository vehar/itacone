itacone
=======

Adding a simple, arduino-like style to peripheral interaction for STM32.  
Добавляем в STM32 простой синтаксис для общения с периферией в стиле ардуино, плюс синтаксический сахар.

Использование:

	include "itacone.h"

	PC8.mode(OUT).write(HIGH); //Переводим пин PC8 в состояние "выход"
	//и устанавливаем его в состояние "1"

	while(1)
	{
		PC8.toggle(); //Переключаем пин PC8 в противоположное состояние
		delayMs(200); //Пауза на 200 миллисекунд
	}

##Модуль GPIO
Используем простые функции для обращения к портам ввода-вывода, а к самым часто используемым есть ещё и алиасы-сокращения. И конечно, не забываем объединять вызовы в цепочки!

####Переменные-пины
У вас сразу есть переменные вроде PA4 или PC13 (типа pin), к которым вы сразу можете обращаться всякими удобными функциями, например так:

```c
PC8.mode(OUTPUT_PP, SPEED_2).high();
```

Можно задать копию переменной пина, сложив её в переменную типа pin, и используя дальше именно эту копию:

```c
pin _pin1=PB3;
_pin1.mode(OUTPUT);
```

Да и совместить всё в одну операцию:

```c
pin _pin2=PD1.mode(INPUT);
```

И наконец, всё это использовать в цикле:

```c
while(1)
{
	_pin1.high();
	sleep(100);
	_pin1.low();
	sleep(100);
	_pin1.out(_pin2.in());
	sleep(500);
}
```

Перечислю все полезные функции.
###Настройка пина
####pin.mode (mode, [speed])
Задаёт режим и скорость работы пина, и инициализирует его.  
```c
pin.mode (OUTPUT_PP | OUTPUT_OD | OUTPUT_AF_PP | OUTPUT_AF_OD |
INPUT_AN | INPUT_FL | INPUT_PU | INPUT_PD, [SPEED_2 | SPEED_10 | SPEED_50]);
//возвращает снова тот же pin
```
####Параметры настройки
OUTPUT_PP - выход в режиме пуш-пулл.  
OUTPUT_OD - выход с открытым коллектором.  
OUTPUT_AF_PP - выход периферии в режиме пуш-пулл.  
OUTPUT_AF_OD - выход периферии с открытым коллектором.  
INPUT_AN - вход для АЦП.  
INPUT_FL - "плавающий" вход, или третье (высокоимпедансное) состояние.  
INPUT_PU - вход со слабой подтяжкой к "+".  
INPUT_PD - вход со слабой подтяжкой к "-".
###Вывод
####pin.out (state) или pin.write(state) или pin.set(state)
Выводит бит в пин - устанавливает пину переданное состояние.  
```c
pin.out (LOW | HIGH);
pin.write (LOW | HIGH);
pin.set (LOW | HIGH);
//возвращает снова тот же pin
```
####pin.high()
Переводит пин в "1".
####pin.low()
Переводит пин в "0".
####pin.toggle()
Переключает пин на противоположное состояние.
###Ввод
Сначала - функции ввода, возвращающие значение состояния. С ними чейнинг невозможен.
####pin.in() или pin.read() или pin.readIn()
Читает состояние пина, настроенного на вход.  
```c
int a = PC6.in();
int b = PA3.read();
int c = PB11.readIn();
//возвращает LOW или HIGH
```
####pin.readOut()
Читает состояние пина, настроенного на выход.  
```c
int c = PA5.readOut();
//возвращает LOW или HIGH
```
Теперь - функции ввода, возвращающие пин. Их можно чейнить.
####pin.in(state) или pin.read(state) или pin.readIn(state)
Читает состояние пина, настроенного на вход.  
```c
int a, b, c;
PC6.in(&a);
PA3.read(&b);
PB11.readIn(&c);
//возвращает обратно пин
```
####pin.readOut()
Читает состояние пина, настроенного на выход.  
```c
int a;
PA5.readOut(&a);
//возвращает пин
```
###Вспомогательные функции
####pin.delayMs(int ms)
Пауза на ms миллисекунд. Удобна тем, что возвращает pin. Не забывайте, что пауза впустую расходует ресурсы процессора, не выполняя полезной работы. По возможности делайте паузы аппаратным таймером.
###Цепочки
Все функции (кроме функций чтения состояния) возвращают pin, поэтому вызовы можно соединять в цепочки:

```c
int a, b;
b = PC8. mode(OUTPUT). high() .delayMs(300). low(). delayMs(500). mode(INPUT). read(&a). delayMs(1000). read();
```

В этом примере мы назначаем пин PC8 "выходом", на 300 миллисекунд выводим в него "1", потом на 500 миллисекунд выводим "0", переводим пин во "вход" и читаем его значение в переменную a, а через секунду - снова читаем состояние и возвращаем его в переменную b.

Это удобно, например, для выполнения в цикле.