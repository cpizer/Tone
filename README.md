!!!!! NOT USABLE YET !!!!!

# Tone Library #

----

## Description ##

This is an Arduino Library to produce square-wave of the specified frequency (and 50% duty cycle) on any Arduino pin.

A duration can optionally be specified, otherwise the wave continues until `stop()` is called.

A count of pulses can be specified after which the pulse generation stops and the count of pulses that were generated since their last `reset()` can be polled.
The count can be incremented on either the positive-edge or the negative edge, depending on the identifier defined by the user.
Using those features, this library is suitable for commanding STEP/DIR stepper drivers.

The pin can be connected to a piezo buzzer or other speaker to play tones.

Be sure to try out the RTTTL (RingTone Text Transfer Language) example!

### Arduino Core Version ###

A simplified version of the Tone library has been incorporated into the Arduino core since 0018.  It only provides a single tone (since only one timer is used).  You can find the core documentation [here](http://arduino.cc/en/Reference/Tone).

Check out the [tutorial](http://itp.nyu.edu/physcomp/labs/labs-arduino-digital-and-analog/tone-output-using-an-arduino/) by Tom Igoe at NYU's Interactive Telecommunications Program (ITP).  It demonstrates how to use the core `tone()` and `noTone()` commands.

### _WARNING_ ###
*_Do not connect the pin directly to some sort of audio input.  The voltage is considerably higher than a standard [line level voltages](http://en.wikipedia.org/wiki/Line_level), and can damage sound card inputs, etc.  You could use a voltage divider to bring the voltage down, but you have been warned._*

*_You MUST have a resistor in line with the speaker, or you WILL damage your controller._*

----

## Download and Installation ##

* [Tone Library - Latest Version](https://github.com/bhagman/Tone/archive/master.zip)

Unzip the folder and rename it to `Tone`, then move it to your `arduinosketchfolder/libraries/` folder.

----

## Library Usage ##

### Instantiation/Creation ###

```arduino
Tone tone1;

void setup(void)
{
  tone1.begin(13);
}

```


### Properties ###
 * No properties.

### Methods ###
 * `begin(`_*`pin`*_`)` - prepares a pin for playing a tone.
 * `isPlaying()` - returns `true` if tone is playing, `false` if not.
 * `play(`_*`frequency`*_` [, `_*`duration`*_`])` - play a tone.
   * _*`frequency`*_ is in Hertz, and the _*`duration`*_ is in milliseconds.
   * _*`duration`*_ is optional.  If _*`duration`*_ is not given, tone will play continuously until _*`stop()`*_ is called.
   * `play()` is [non-blocking](http://en.wikipedia.org/wiki/Non-blocking_synchronization).  Once called, `play()` will return immediately. If _*`duration`*_ is given, the tone will play for that amount of time, and then stop automatically.
 * `playNPulses(`_*`frequency`*_`, `_*`pulses`*_`)` - play a tone until the defined amount of pulses was generated
   * _*`frequency`*_ is in Hertz.
   * _*`duration`*_ defines the count of pulses that is generated until the generation stops automatically.
   * * `playNPulses()` is [non-blocking](http://en.wikipedia.org/wiki/Non-blocking_synchronization).  Once called, `playNPulses()` will return immediately.
 * `getCounter()` - returns the counter's current value.
 * `resetCounter()` - reset the instance' counter to 0.
 * `stop()` - stop playing a tone.

### Constants ###

Depending on the preferences, the pulses are counted on either their positive or negative edge. Per default, positive edges are counted. The following identifiers can be used to control the behaviour:

`#TONE_COUNT_POSITIVE_EDGE` and `#TONE_COUNT_NEGATIVE_EDGE`

### Quick example ###

Play a 440 Hz on pin 13:
```arduino
#include <Tone.h>

Tone tone1;

void setup()
{
  tone1.begin(13);
  tone1.play(440);
}

void loop()
{
}
```

----

## Ugly Details ##

The library uses the hardware timers on the microcontroller to generate square-wave tones in the audible range.

You can output the tones on any pin (arbitrary).  The number of tones that can be played simultaneously depends on the number of hardware timers (with CTC capability) available on the microcontroller.

 * ATmega8: 2 (timers 2, and 1)
 * ATmega168/328: 3 (timers 2, 1, and 0)
 * ATmega1280: 6 (timers 2, 3, 4, 5, 1, 0)

The timer order given above is the order in which the timers are allocated.  Timer 0 is a sensitive timer on the Arduino since it provides `millis()` and PWM functionality.

The range of frequencies that can be produced depends on the microcontroller clock frequency and the timer which is being used:

|*MCU clock*|*8 bit timer F<sub>low</sub>*|*16 bit timer F<sub>low</sub>*|*F<sub>high</sub>*|
|-----------|--------------------|---------------------|---------|
|8 MHz      |16 Hz               |1 Hz (1/16 Hz)       |4 MHz    |
|16 MHz     |31 Hz               |1 Hz (1/8 Hz)        |8 MHz    |

Although F<sub>high</sub> can go as high as 8 MHz, the [human hearing range](http://en.wikipedia.org/wiki/Hearing_range#Humans) is typically as high as 20 kHz.

Tone accuracy is dependent on the timer prescalar.  Frequency quantization occurs as the frequencies increase per prescalar.

If you used a 16 bit timer (e.g. timer 1, or timers 3,4,5 on '1280), you could generate "tones" down to 1/8 Hz (one cycle every 8 seconds), although the library only accepts integers for frequency.

After all is said and done, because `play()` only accepts unsigned integers for frequency, the maximum frequency that can be produced is 65535 Hz - which, after rounding, results in a 65573.77 Hz "tone" on a 16 MHz part.  Even if play accepted larger values for frequency, you couldn't achieve better than around 80KHz with the Tone library because the pin toggling is done in software.  Each toggle, in software, requires _AT LEAST_ 50+ cycles.

----

## Links ##

### Projects/Examples Using Tone Library and Wiring Framework Version of `tone()` ###

  * [Tom Igoe - Tone Output using an Arduino](http://itp.nyu.edu/physcomp/labs/labs-arduino-digital-and-analog/tone-output-using-an-arduino/)
  * [Evan Kale - Build Your Own Metal Detector with an Arduino](http://www.allaboutcircuits.com/projects/metal-detector-with-arduino/)

### RTTTL Sites ###

  * http://merwin.bespin.org/db/rts/index.php
  * http://www.cellringtones.com/
  * http://arcadetones.emuunlim.com/

### Other ###

  * [Arduino sketch for high frequency precision sine wave tone sound synthesis](http://www.adrianfreed.com/content/arduino-sketch-high-frequency-precision-sine-wave-tone-sound-synthesis)
