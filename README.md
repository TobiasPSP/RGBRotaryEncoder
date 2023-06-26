# Using RGB Rotary Encoder (I2C) via ESP8266

Rotary encoders are highly useful as input device, i.e. to control a menu system in combination with some sort of display, or control ranges such as color range, volume, etc.

> [Rotary Encoders](https://en.wikipedia.org/wiki/Rotary_switch) look similar to classic [potentiometers](https://en.wikipedia.org/wiki/Potentiometer) but they work completely different: the knob can be turned in both directions endlessly. Each rotation sends an impulse that can be received and interpreted by a microcontroller. While a potentiometer changes resistance in a given range, a Rotary Encoder is a simple input device that registers increments and decrements.

In this repo, I wrote down my steps to get going with a special Rotary Encoder from [duppa.net](https://www.duppa.net/shop/i2cencoder-v2-1-with-soldered-accessory/) (not affiliated in any way). Here is what distinguishes this particular Rotary Encoder from plain vanilla Rotary Encoders, and why I found that important:

* **Just two wires (GPIOs) required**: the Rotary Encoder comes presoldered on a PCB with logic to control it via *I2C*, so you only need two wires to hook it up to your microcontroller. That's important because classic Rotary Encoders need five (5) wires, and GPIO pins are always scarce, especially when it comes to *ESP8266*.
* **No extra debouncing needed**: With classic Rotary Encoders, you need to invest considerable thoughts in how to debounce the mechanical switches inside the Rotary Encoder. If you use a raw Rotary Encoder as-is and connect its switches to your GPIOs, you will see typical bounce-effects when one rotation counts for more than one increment or decrement. The Rotary Encoder discussed here comes with a ready-to-use PCB that already uses hardware (and software in its accompanying library) to debounce the switches.
* **3.3V-5V**: The PCB can be used in a wide voltage range of 3.3-5V so it is equally suited for classic *Arduinos* and modern *ESPxxxx* microcontrollers.
* **Can also be used as a (confirmation) switch**: This Rotary Encoder comes with *Switch Functionality* so you can not only turn it, but also press it. This is important for scenarios where you want to use the Encoder Knob to control a menu system. Pressing the knob confirms user input. Simple Rotary Encoders have no switch functionality so you can just turn the knob but not confirm your action in any way.
* **No conflicts with other I2C devices and their addresses**: The PCB provides solder bridges to assign individual I2C addresses to each Rotary Controller, making it easy to use many Rotary Encoders in your project (or be flexible when using other I2C devices with fixed I2C addresses in your project, i.e. displays).

In addition to the pure Rotary Encoder logic, the PCB comes with valuable extra features:

* **Illuminated transparent RGB Knob**: a special highlight is the presoldered transparent Rotary Encoder knob that is placed on top of a RGB LED. As an added bonus, you can control the illumination of the knob via I2C. Being able to turn on and off and choose color of the knob illumination is an extra benefit that makes this human interface even more intuitive. For example, when you use this RC to control a menu system, you could switch colors from green to red, depending on whether a selection is allowable or not.
* **Fading**: Both the built-in RGB LED and the additional freely usable GPIOs support fading voa PWM, so the light can be slowly turned on and off.
* **Gamma Correction**: Both the built-in RGB LED and the additional freely usable GPIOs support gamma correction so the produced colors can be adapted to the human eye and produce more natural colors that truly match the reception.
* **Additional GPIOs**: As previously mentioned, GPIO pins are scarce and valuable for most small microcontrollers. So using I2C with its requirement of just 2 GPIOs is already a big plus for the Rotary Encoder discussed here. In addition, the PCB comes with *two additional* GPIOs that you can freely use and control via I2C. These GPIOs can be used as digital and analog input or as PWM output.
* **Permanently storing settings (EEPROM)**: The PCB even comes with 256 bytes of EEPROM storage so you can store settings permanently.

## Basic Wiring

To start working with this Rotary Encoder, at minimum you need to connect it with power plus the two I2C connections (**SCL** and **SDA**). 

> It is highly recommeded to also connect the **INT** pin and be able to receive interrupts so the Rotary Encoder *can efficiently inform you* (**PUSH**) should something interesting happen (such as the user rotating the knob or pressing the switch).
>
> Without interrupt, your code would have to *query the PCB all the time in short intervals* (**PULL**) to find out its state, resulting in a high CPU load, high energy consumption, and high I2C data congestion.

The PCB features five connectors on each side. You can use connectors on the side that suits you best. Since power lines and I2C are *bus*-like systems, you can easily daisy-chain multiple PCBs if you need more than one Rotary Encoder. 

* **-**: connects to ground (**GND**)
* **+**: connects to positive voltage (3.3-5V)
* **SDA**: I2C data, connects to *D2* on *Wemos D1 Mini ESP8266* (make sure you connect to the correct I2C pin for your particular microcontroller)
* **SCL**: I2C clock, connects to *D1* on *Wemos D1 Mini ESP8266* (make sure you connect to the correct I2C pin for your particular microcontroller)
* **INT**: Interrupt, connect to input-enabled pin on your microcontroller. This pin is tied low when an interrupt occurs (i.e. the know was turned or the switch was pressed)

In addition, if you'd like to take advantage of the additional free-to-use GPIOs, you can connect wires to the four smaller connector holes on one side of the presoldered Rotary Encoder:

* **GP1**: can be configured as digital or analog input or as (PWM) output
* **VCC**: connects to positive voltage
* **GP3**: can be configured as digital or analog input or as output (no PWM)
* **GP2**: can be configured as digital or analog input or as (PWM) output

