---
layout: post
title: "Haskell and Hardware for the Holidays"
date: 2010-12-18
categories: 
  - "copilot"
  - "embedded-software"
  - "hardware"
  - "haskell"
  - "software"
  - "verification"
---

Looking to make a statement this holiday season?  You could try to win the office "ugly holiday sweater" contest.  Or, you could play "Jingle Bells" on your Arduino microcontroller, using Haskell.  This post is about the latter.

We're going to write this small program using the Copilot embedded domain-specific language (on [Hackage](http://hackage.haskell.org/package/copilot) and the source on [Github](https://github.com/leepike/Copilot)).  Copilot is a stream language that allows you to generate embedded C code from programs written essentially as Haskell lists (using [Atom](http://hackage.haskell.org/package/atom) as a backend for the C code generation).  This post is about how to use Copilot/Haskell (v. 1.0) to make your embedded C programming easier and more likely to be correct.  Here's what we're going to do---please don't look too closely at my soldering, and turn the volume up, since a piezo speaker isn't loud:

[Youtube video](https://www.youtube.com/watch?v=bN-VEGoiKlg)

(For the impatient, the Haskell file is [here](https://gist.github.com/747707), and the generated `.c` and `.h` files are [here](https://gist.github.com/747710) and [here](https://gist.github.com/747711), respectively.)

We're going to essentially recreate [this](http://www.arduino.cc/en/Tutorial/PlayMelody) C/Wiring program, plus flash some LEDs, but hopefully in a easier, safer way.  We need to manage three tasks:

1. Determine the note and number of beats to play.
2. Play the piezo speaker.
3. Flash the LEDs.

We'll start by defining which pins control what function:

```
-- pin numbers
speaker, red, green :: Spec Int32
speaker = 13
red     = 12
green   = 11
```

The type `Spec Int32` takes an `Int32` and lifts it into a Copilot expression.

We'll call the program `cycleSong`. The type of a Copilot program is `Streams`, which is a collection of `Spec a`\`s, and it resides within the Writer Monad. First, we'll declare some variables.

```
cycleSong :: Streams
cycleSong = do
  -- Copilot vars
  let idx       = varI32 "idx"
      notes     = varI32 "notes"
      duration  = varI32 "duration"
      odd       = varI32 "odd"
      even      = varI32 "even"
      playNote  = varB   "playNote"
  -- external vars
      note = extArrI32 "notes" idx
      beat = extArrI32 "beats" idx
```

There are two classes of variables: Copilot variables that will refer to streams (infinite lists), and external variables, which can refer to data from C (including the return values of functions, global variables, and arrays). The constructors are mnemonics for the type of the variables; for example, `varI32` is a variable that will refer to a stream of `Int32`s. Similarly, `extArrI32` is an external variable referring to a C array of `Int32`s (i.e., `int32_t`). Notice the `idx` argument---it is the stream of values from which the index into the array is drawn (constants can also be used for indexes).

Now for the actual program:

```
 idx      .= [0] ++ (idx + 1) `mod` (fromIntegral $ length notesLst)
 notes    .= note
 duration .= beat * 300
 odd      .= mux (idx `mod` 2 == 1) green red
 even     .= mux (idx `mod` 2 == 1) red green
 playNote .= true
 -- triggers
 trigger playNote "digitalWrite" (odd <> true)
 trigger playNote "digitalWrite" (even <> false)
 trigger playNote "playtone" (speaker <> notes <> duration)
```

And that's basically it.  There are two parts to the program, the definition of Copilot streams, which manage data-flow and control, and triggers, which call an external C function when some property is true.  Copilot streams look pretty much like Haskell lists, except that functions are automatically lifted to the stream level for convenience.  Thus, instead of writing,

```
 x = [0] ++ map (+1) x
```

in Copilot, you simply write

```
 x .= [0] ++ x + 1
```

Similarly for constants, so the Copilot stream definition

```
playNote .= true
```

lifts the constant `true` to an infinite stream of `true` values. The function `mux` is `if then else`\---`mux` refers to a [2-to-1 multiplexer](http://en.wikipedia.org/wiki/Multiplexer). So that means that the stream `odd` takes the value of `green` when `idx` is odd, and `red` otherwise, where `green` and `red` refer to the pins controlling the respective LEDs.

Just to round out the description of the other defined streams, `idx` is the index into the C arrays containing the notes and beats, respectively---that's why we perform modular arithmetic. The stream `duration` tells us how long to hold a note; 300 is a magic "tempo" constant.

Now for the triggers. Each of our triggers "fires" whenever the stream `playNote` is true; in our case, because it is a constant stream of trues, this happens on each iteration. So on each iteration, the C functions `digitalWrite` and `playTone` are called with the respective function arguments ('`<>`' separates arguments). The function `digitalWrite` is a function that is part of the [Wiring language](http://wiring.org.co/), which is basically C with some standard libraries, from which `digitalWrite` comes. We'll write `playTone` ourselves in a second.

## The C Code

We need a little C code now.  We could write this directly, but we'll just do this in Haskell, since there's so little we need to write---the Copilot program handles most of the work.  But a caveat: it's a little ugly, since we're just constructing Haskell strings. Here are [a few functions](http://leepike.github.com/Copilot/doc/Language-Copilot-AdHocC.html) (included with Copilot) to make this easier, and here are [some more](https://github.com/norm2782/blink.hs/blob/master/CHS.hs). (If someone properly writes a package to write ad-hoc C code from Haskell, please leave a comment!)

First, we need more magic constants to give the frequency associated with notes (a space is a rest).

```
freq :: Char -> Int32
freq note  =
  case note of
    'c' -> 1915
    'd' -> 1700
    'e' -> 1519
         ...

```

and here are the notes of the song and the beats per note:

```
jingleBellsNotes = "eeeeeeegcdefffffeeeddedgeeeeeeegcdefffffeeggfdc"
jingleBellsBeats =
  [ 1,1,2  , 1,1,2, 1,1,1,1, 4
  , 1,1,1,1, 1,1,2, 1,1,1,1, 2,2
  , 1,1,2  , 1,1,2, 1,1,1,1, 4
  , 1,1,1,1, 1,1,2, 1,1,1,1, 4
  ]
```

The other main piece of C code we need to write is the function `playtone`. The piezo speaker is controlled by pulse width modulation, basically meaning we'll turn it on and off really fast to simulate an analogue signal. Here is it's definition (using a little helper Haskell function to construct C functions):

```
    [ function "void" "playtone" ["int32_t speaker", "int32_t tone", "int32_t duration"] P.++ "{"
    , "#ifdef CBMC"
    , "  for (int32_t i = 0; i < 1; i ++) {"
    , "#else"
    , "  for (int32_t i = 0; i < duration * 1000; i += tone * 2) {"
    , "#endif"
    , "    digitalWrite(speaker, HIGH);"
    , "    delayMicroseconds(tone);"
    , "    digitalWrite(speaker, LOW);"
    , "    delayMicroseconds(tone);"
    , "  }"
    , "}"
    ]
```

`HIGH`, `LOW`, `digitalWrite`, and `delayMicroseconds` are all part of the Wiring standard library.  That `ifdef` is for verification purposes, which we'll describe in just a bit.

Besides a little more cruft, that's it!

## Test, Build, Verify

"Jersey Shore" may have introduced you to the concept of _gym, tan, laundry_, but here we'll stick to _test, build, verify_.  That is, first we'll test our program using the Copilot interpreter, then we'll build it, then we'll prove the memory safety of the generated C program.

- _Interpret._ We define a function that calls the Copilot interpreter:
    
    ```
    interpreter =
      interpret cycleSong 20
        $ setE (emptySM {i32Map = fromList [ ("notes", notesLst)
                                           , ("beats", beatsLst)]})
        baseOpts
    ```
    
    This calls the Copilot interpreter, saying to unroll `cycleSong` 20 times. Because the Copilot program samples some external C values, we need to provide that data to the interpreter. Fortunately, we have those arrays already defined as Haskell lists. Executing this, we get the following:
    
    ```
    period: 0   duration: 300   even: 11   idx: 0   notes: 1519   odd: 12   playNote: 1
    period: 1   duration: 300   even: 12   idx: 1   notes: 1519   odd: 11   playNote: 1
    period: 2   duration: 600   even: 11   idx: 2   notes: 1519   odd: 12   playNote: 1
    period: 3   duration: 300   even: 12   idx: 3   notes: 1519   odd: 11   playNote: 1
    period: 4   duration: 300   even: 11   idx: 4   notes: 1519   odd: 12   playNote: 1
    period: 5   duration: 600   even: 12   idx: 5   notes: 1519   odd: 11   playNote: 1
    period: 6   duration: 300   even: 11   idx: 6   notes: 1519   odd: 12   playNote: 1
                                                   . . .
    ```
    
    Good, it looks right. (`period` isn't a Copilot variable but just keeps track of what round we're on.)
- _Build._ To build, we generate the C code from the Copilot program, then we'll use a cross-compiler targeting the [ATmega328](http://www.arduino.cc/en/Main/ArduinoBoardDuemilanove). The easiest way (I've found) is via Homin Lee's [Arscons](http://code.google.com/p/arscons/). Arscons is based on [Scons](http://www.scons.org/), a Python-based build system. Arscons makes three assumptions: (1) the program is written as a Wiring program (e.g., there's a `loop()` function instead of a `main()` function is the main difference), (2) the extension of the Wiring program is `.pde`, and (3) the directory containing the `XXX.pde` is `XXX`. For us, all that really means is that we have to change the extension of the generated program from `.c` to `.pde`. So we define
    
    ```
    main :: IO ()
    main = do
      compile cycleSong name
        $ setPP cCode  -- C code for above/below the Copilot program
        $ setV Verbose -- Verbose compilation
        baseOpts
      copyFile (name P.++ ".c") (name P.++ ".pde") -- SConstruct expects .pde
    ```
    
    and then execute
    
    ```
    > runhaskell CopilotSong.hs
    ```
    
    to do this.
    
    To build the executable, we issue
    
    ```
    > scons
    ```
    
    then
    
    ```
    scons upload
    ```
    
    when we're ready to flash the microcontroller.
- _Verify._ Is the generated C program memory safe?  Wait... What do I mean by 'memory safe'?  I'll consider the program to be memory safe if the following hold:
    
    - No arithmetic underflows or overflows.
    - No floating-point not-a-numbers (NaNs).
    - No division by zero.
    - No array bounds underflows or overflows.
    - No Null pointer dereferences.
    
    Of course this is an approximates memory-safety, but it's a pretty good start. If the compiler is built correctly, we should be pretty close to a memory-safe program. But we want to check the compiler, even though Haskell's type system gives us some evidence of guarantees already. Furthermore, the compiler knows nothing about arbitrary C functions, and it doesn't know how large external C arrays are.
    
    We can _prove_ that the program is memory safe. We call out to [CBMC](http://www.cprover.org/cbmc/), a C model-checker developed primarily by Daniel Kröning. This is whole-program analysis, so we have to provide the location of the libraries. We define
    
    ```
    verifying :: IO ()
    verifying =
      verify (name P.++ ".c") (length notesLst * 4 + 3)
        (     "-DCBMC -I/Applications/Arduino.app/Contents/Resources/Java/hardware/arduino/cores/arduino/ "
         P.++ "-I/Applications/Arduino.app/Contents/Resources/Java/hardware/tools/avr/avr-4/include "
         P.++ "--function cbmc")
    ```
    
    which calls `cbmc` on our generated C program. Let me briefly explain the arguments. First we give the name of the C program.
    
    Then we say how many times to unroll the loops. This requires a little thinking. We want to unroll the loops enough times to potentially get into a state where we might have an out of bounds array access (recall that the Copilot stream `idx` generates indexes into the arrays). The length of the C arrays are given by `length notesLst`. When compiling the Copilot program (calling the module's `main` function, a periodic schedule is generated for the program). From the schedule, we can see that `idx` is updated every fourth pass through the loop. So we unwind it enough loop passes for the counter to have the opportunity to walk off the end of the array, plus a few extra passes for setup. This is a minimum bound; you could of course over-approximate and unroll the loop, say, 1000 times.
    
    Regarding loop unrolling, remember that `#ifdef` from the definition of `playtone()`? We include that to reduce the difficulty of loop unrolling. `playtone()` gets called on every fourth pass through the main loop, and unrolling both loops is just too much for symbolic model-checking (at least on my laptop). So we give ourselves an informal argument that the loop in `playtone()` won't introduce any memory safety violations, and the `#ifdef` gives us one iteration through the loop if we're verifying the system. A lot of times with embedded code, this is a non-issue, since loops can just be completely unrolled.
    
    The `-D` flag defines a preprocessor macro, and the `-I` defines a include path. Finally, the `--function` flag gives the entry point into the program. Because we generated a Wiring program which generates a `while(1)` loop for us through macro magic, we have to create an explicit loop ourselves for verification purposes.
    
    If you're interested in seeing what things look like when they fail, change the `idx` stream to be
    
    ```
      idx .= [0] ++ (idx + 1)
    
    ```
    
    and `cbmc` will complain
    
    ```
    Violated property:
      file CopilotSing.c line 180 function __r11
      array `beats' upper bound
      (long int)__1 < 47
    
    VERIFICATION FAILED
    
    ```
    

So that's it. Happy holidays!
