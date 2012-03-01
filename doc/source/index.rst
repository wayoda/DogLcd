.. DogLcd documentation master file, created by
   sphinx-quickstart on Wed Feb 29 16:40:43 2012.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

Welcome to DogLcd's documentation!
==================================

Contents:

.. toctree::
   :maxdepth: 2

The library code is based on the code of the LiquidCrystal-library that was shipped with Arduino-0017. 
I used the same names for methods and parameters to make it easy to port code written for the 
LiquidCrystal-library to the DogLcd lib.

The library-code has been tested on every version since Arduino-0017.

.. figure::  images/DogLcd_Hello.jpg
   :align:   center


 
Installation
------------
Download the latest version of the DogLcd library and unzip it in the 
``libraries`` folder of your sketchbook directory. 

Verify your installation by opening and compiling the ``HelloWorld`` Sketch 
from the DogLcd examples folder. If you get no errors here, the installation should be ok.

Library Documentation
---------------------
All methods that are implemented by the DogLcd-library are documented on the [Library]-page. 
But for a quick start I'd like to go through a simple example. 

The Dog says `Hello World`
**************************
The fastest way to get you started is probably to go through the ``HelloWorld``
 example line by line. Here is the exciting code

::

	/*
	  DogLcd Library - Hello World
	  Demonstrates the basic use of a DOGM text LCD display.
	  This sketch prints "Hello World!" to the LCD
	  and shows the time.
	     
	  See the hardware-documention for wiring instuctions
	  We assume  the following pins are connected:
	  LCD SI pin to digital pin 2
	  LCD CLK pin to digital pin 3
	  LCD RS pin to digital pin 4
	  LCD CSB pin to digital pin 5
	  LCD RESET pin is not used
	  LCD Backlight pin is not used
        */
	// include the library code:
	#include <DogLcd.h>
	// initialize the library with the numbers of the interface pins
	DogLcd lcd(2, 3, 4, 5);

	void setup() {
	     // set up the LCD type and the contrast setting for the display 
	     lcd.begin(DOG_LCD_M162);
	     // Print a message to the LCD.
	     lcd.print("hello, world!");
	}
	
	void loop() {
	     // set the cursor to column 0, line 1
	     // (note: line 1 is the second row, since counting begins with 0):
	     lcd.setCursor(0, 1);
	     // print the number of seconds since reset:
	     lcd.print(millis()/1000);
	}
	
					     

The comment at the top tells you which Arduino-Pins have to be wired to the pins 
on the display to make the sketch work as expected. If you want/have to use 
different Arduino-pins I'll show later what needs to be changed in the example-code. 

As with every Arduino library we habe to include the library header.

``#include <DogLcd.h>``

Then we create a new static DogLcd variable by calling the constructor 


``DogLcd lcd(2, 3, 4, 5);``

The four parameters refer to the pins on the Arduino that drive 
(in that order) the `SI,CLK,RS,CSB` lines on the display. 
If you need to change any of the Arduino-pins you have to do it here. 
But the display is not ready for use now. We have only told the library 
which Arduino pins are to be used to communicate with the display.

In ``setup()`` we have to call the ``begin()`` method of the library 
to initialize the display. 

::
  // set up the LCD type and the contrast setting for the display 
  lcd.begin(DOG_LCD_M162);

``begin()`` takes up to three arguments, but only one of them is required.   
* The type of display you connected which is identified by one of the constants
* *DOG_LCD_M081* A display with 1 lines 8 characters  
* *DOG_LCD_M162* A display with 2 lines 16 characters  
* *DOG_LCD_M163* A display with 3 lines 16 characters  

The second argument is the contrast setting for display. Values between 
0..63 (dec) or 0x00..0x3f (hex) are allowed here. The default value (0x28) 
that is automatically selected when no argument is given, works fine with 
most combinations of displays and backlights. 
The next example shows the usage of the method when you want to set the 
contrast to a specific value.

The method ``begin()`` will return -1 if any of the arguments is not valid.

Internally the display is reset and now ready for use!

``lcd.print("hello, world!");`` in ``setup()`` will display the string on the first display line.

In ``loop()`` the cursor is positioned on the start of the second line and 
the number of seconds passed since the sketch was started is printed until you pull the plug.
::
	lcd.setCursor(0, 1);
	lcd.print(millis()/1000);


That's it. (Its only "Hello World" in the end)

Adding a second display
***********************
The [Hardware]-page shows how to wire a second display to your Arduino 
and here is a little example sketch that alternately prints a short string to each of the displays.
::
	/*
	  DogLcd Library - PingPong

 	  Demonstrates the basic use two DOGM text LCD displays.
	  This sketch prints "Ping" and "Pong" alternately to the displays.
 	  See the hardware documentation for wiring instuctions
	  We assume  the following pins are connected:
	  For boths displays SI pin to digital pin 2
	  For boths displays CLK pin to digital pin 3
  	  For boths displays RS pin to digital pin 4
  	  For the "Ping" display" the CSB pin is connected to pin 5
  	  For the "Pong" display" the CSB pin is connected to pin 6
  	  For boths displays the RESET pin is not used (connected to +5V)
  	  For boths displays the Backlight switching is not used
	*/
	// include the library code:
	#include <DogLcd.h>

	// Ping : initialize the library with the numbers of the interface pins
	DogLcd ping(2, 3, 4, 5);
	// Pong : initialize the library with the numbers of the interface pins
	DogLcd pong(2, 3, 4, 6);

	void setup() {
	     // set up the LCD type and the contrast setting for ping 
	     ping.begin(DOG_LCD_M162,0x28);
	     // set up the LCD type and the contrast setting for ping 
	     pong.begin(DOG_LCD_M162,0x28);
	     //clear both displays
	     ping.clear();
	     pong.clear();
	}

	void loop() {
	     //clear the ping-display
	     ping.print("PING");
	     delay(500);
	     ping.clear();
	     pong.print("PONG");
	     delay(500);
	     pong.clear();
	}


Since we send data to each display individually we need two DogLcd 
variables ("ping" and "pong"). The first three arguments to the constructor 
are the same because these are the common data-lines on the bus. 
Only the last argument `CSB` is different for each display. 
It is wired to pin 5 for the first display and to pin 6 for the second one. 
::
	DogLcd ping(2, 3, 4, 5);
	DogLcd pong(2, 3, 4, 6);


Both displays also have to be initialized individually. In my example 
I used two 3-line  displays of the same type, but you can mix 1-, 2- 3-line displays as you like. 
This time we specify an individual contrast setting for each of the displays.
::
	ping.begin(DOG_LCD_M162,0x1F);
	pong.begin(DOG_LCD_M162,0x28);

Printing to the displays individually is simply a matter of calling the `print()`-method for the right display. 
::
	ping.print("PING");
	pong.print("PONG");


.. figure::  images/DogLcd_PingPong.jpg
   :align:   center

   Here is a picture of my Arduino Nano driving 2 displays with the above code


Indices and tables
==================

* :ref:`genindex`
* :ref:`modindex`
* :ref:`search`

