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

`
PC8.mode(OUTPUT_PP, SPEED_2).high();
`

Можно задать копию переменной пина, сложив её в переменную типа pin, и используя дальше именно эту копию:

`
pin _pin1=PB3;
_pin1.mode(OUTPUT);
`

Да и совместить всё в одну операцию:

`
pin _pin2=PD1.mode(INPUT);
`

И наконец, всё это использовать в цикле:

`
	while(1)
	{
		_pin1.high();
		sleep(100);
		_pin1.low();
		sleep(100);
		_pin1.out(_pin2.in());
		sleep(500);
	}
`

Перечислю все полезные функции.
####pin.mode (mode, [speed])
Задаёт режим и скорость работы пина, и инициализирует его.  
`
	pin.mode (OUTPUT_PP | OUTPUT_OD | OUTPUT_AF_PP | OUTPUT_AF_OD |
	INPUT_AN | INPUT_FL | INPUT_PU | INPUT_PD, [SPEED_2 | SPEED_10 | SPEED_50]);
		//возвращает снова тот же pin
`
####pin.out (state) или pin.write(state) или pin.set(state)
Выводит бит в пин - устанавливает пину переданное состояние.  
`
	pin.out (LOW | HIGH);
	pin.write (LOW | HIGH);
	pin.set (LOW | HIGH);
		//возвращает снова тот же pin
`
####pin.high()
Переводит пин в "1".
####pin.low()
Переводит пин в "0".
####pin.toggle()
Переключает пин на противоположное состояние.
####pin.in() или pin.read() или pin.readIn()
Читает состояние пина, настроенного на вход.  
`
	int a = PC6.in();
	int b = PA3.read();
	int c = PB11.readIn();
		//возвращает LOW или HIGH
`
####pin.readOut()
Читает состояние пина, настроенного на выход.
`
	int c = PA5.readOut();
		//возвращает LOW или HIGH
`
####pin.delayMs(int ms)
Пауза на ms миллисекунд. Удобна тем, что возвращает pin.
####Цепочки
Все функции (кроме функций чтения состояния) возвращают pin, поэтому вызовы можно соединять в цепочки:

`PC8.mode(OUTPUT).high().delayMs(300).low().delayMs(300).mode(INPUT);`