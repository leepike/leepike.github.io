---
layout: post
title: "Twinkle Twinkle Little Haskell"
date: 2010-06-01
categories: 
  - "embedded-software"
  - "haskell"
---

It's been a few months almost a year(!) since John Van Enk [showed us](http://blog.sw17ch.com/wordpress/?p=111) how to twinkle (blink) an LED on his [Arduino](http://arduino.cc/en/Main/HomePage) microcontroller using Atom/Haskell.  Since that time, [Atom](http://hackage.haskell.org/package/atom) (a Haskell embedded domain-specific language for generating constant time/space C programs) has undergone multiple revisions, and the standard Arduino tool-chain has been updated, so I thought it'd be worthwhile to "re-solve" the problem with something more streamlined that should work today for all your Haskell -> Arduino programming needs.  With the changes to Atom, we can blink a LED with just a couple lines of core logic (as you'd expect given the simplicity of the problem).

For this post, I'm using

- [Atom 1.0.4](http://hackage.haskell.org/package/atom)
- The [Arduino Duemilanove](http://www.arduino.cc/en/Main/ArduinoBoardDuemilanove) (ATmega328)---pretty common, as of 2009.
- The Arduino [0018 tool-chain](http://code.google.com/p/arduino/) (on Mac OS X).  This can also be downloaded from the [Arduino website](http://arduino.cc/en/Main/Software).

If you've played with the Arduino, you've noticed how nice the integrated IDE/tool-chain is.  Ok, the editor leaves everything to be desired, but otherwise, things just work.  The language is basically C with a few macros and Atmel AVR-specific libraries (the family to which Arduino hardware belongs).

What we'll do is write a Haskell program AtomLED.hs and use that to generate AtomLED.c.  From that, the scons script will take care of the rest.

## The Core Logic

Here's the core logic we use for blinking the LED from Atom:

```
ph :: Int
ph = 40000 -- Sufficiently large number of ticks (the Duemilanove is 16MHz)

blink :: Atom ()
blink = do
  on <- bool "on" True -- Declare a Boolean variable on, initialized to True.

  -- At period ph and phase 0, do ...
  period ph $ phase 0 $ atom "blinkOn" $ do
    call "avr_blink"        -- Call a locally-defined C function, blink().
    on <== not_ (value on)  -- Flip the Boolean.

  period ph $ phase (quot ph 8) $ atom "blinkOff" $ do
    call "avr_blink"
    on <== not_ (value on)
```

And that's it!  The blink function has two rules, "blinkOn" and "blinkOff".  Both rules execute every 40,000 ticks.  (A "tick" in our case is just a local variable that's incremented, but it could be run off the hardware clock.  Nevertheless, we still know we're getting nearly constant-time due to the code Atom generates.)  The first rule starts at tick 0, and executes at ticks 40,000, 80,000, etc., while the second starts at tick 40,000/8 = 5000 and executes at ticks 5000, 45,000, 85,000, etc.  In each rule, after calling the avr\_blink() C function (we'll define), it modulates a Boolean upon which blink() depends. Thus, the LED is on 1/8 of the time and off 7/8 of the time. (If we wanted the LED to be on the same amount of time as it is off, we could have done the whole thing with one rule.)

## The Details

Really the only other thing we need to do is add a bit of C code around the core logic.  Here's the listing for the C code stuck at the beginning, written as strings: `[ (varInit Int16 "ledPin" "13") -- We're using pin 13 on the Arduino. , "void avr_blink(void);" ]` and here's some code for afterward: `[ "void setup() {" , " // initialize the digital pin as an output:" , " pinMode(ledPin, OUTPUT);" , "}" , "" , "// set the LED on or off" , "void avr_blink() { digitalWrite(ledPin, state.AtomLED.on); }" , "" , "void loop() {" , " " ++ atomName ++ "();" , "}" ]`

The IDE tool-chain expects there to be a setup() and loop() function defined, and it then pretty-prints a main() function from which both are called. The code never returns from loop().

To blink the LED, we call digitalWrite() from avr\_blink(). digitalWrite() is provided by the [Arduino language](http://arduino.cc/en/Reference/HomePage).  (In John's post, he manipulated the [port registers](http://www.arduino.cc/en/Reference/PortManipulation) directly, which is faster and doesn't rely on the Arduino libraries, but it's also lower-level and less portable between Atmel controllers.)  Atom-defined variables are stored in a struct, so state.AtomLED.on references the Atom Boolean variable defined earlier.

## Make it Work!

Now just drop the scons script into the directory (the directory must have the same name as the Haskell file, dropping the extension), and run `> runhaskell AtomLED.hs > scons > scons upload` And your Haskell should be twinkling your LED. runhaskell AtomLED.hs invokes the Atom compile function to generate a C file and headers; scons invokes the build script to build an ELF image to upload, and scons upload again invokes the compiler to upload to your board.

This should work for any Atom-generated program you want to run on your Arduino (modulo deviations from the configuration I mentioned initially). Also, note the conventions and parameters to set in the scons script.

Post if you have any problems, and I might be able to help. Also, I'd love to package the boilerplate up into a "backend" for Atom, but if you have time, please beat me to it.  Thanks.
