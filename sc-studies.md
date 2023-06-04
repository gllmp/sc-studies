# SuperCollider Studies

`https://doc.sccode.org/Tutorials/Getting-Started`

## Functions

SC has no operator precedence, i.e. math operations are done in left-to-right order, and division and multiplication are not done first. To force an order use parentheses. e.g. 4 + (2* 8)

--

## Variables

The letters a to z are what are called interpreter variables. These are pre-declared when you start up SC, and have an unlimited, or "global", scope. This makes them useful for quick tests or examples.

--

## Multichannel

Arrays are used to represent multichannel audio

```
{ [SinOsc.ar(440, 0, 0.2), SinOsc.ar(442, 0, 0.2)] }.play;
```

Multichannel expansion:
if you plug an Array into one of a UGen's arguments, you get an Array of that UGen instead of a single one

```
{ SinOsc.ar([440, 442], 0, 0.2) }.play;
```

The Mix class will mix an array of channels down to a single channel or an array of arrays of channels down to a single array of channels. 

```
// one channel
{ Mix.new([SinOsc.ar(440, 0, 0.2), Saw.ar(660, 0.2)]).postln }.play;

// combine two stereo arrays
(
{
    var a, b;
    a = [SinOsc.ar(440, 0, 0.2), Saw.ar(662, 0.2)];
    b = [SinOsc.ar(442, 0, 0.2), Saw.ar(660, 0.2)];
    Mix([a, b]).postln;
}.play;
)
```

Mix also has another class method called fill, which takes two arguments. The first is a number, which determines how many times the second argument, a Function, will be evaluated. The results of the evaluations will be summed. 

```
(
    var n = 8;
    { Mix.fill(n, { SinOsc.ar(500 + 500.0.rand, 0, 1 / n) }) }.play;
)
```

--

## UGen

UGens represent calculations with signals. They are the basic building blocks of synth definitions on the server, and are used to generate or process both audio and control signals. The many subclasses of UGen are the client-side representations of unit generatos, and are used to specify their parameters when constructing synth definitions.

--

## SynthDef

A SynthDef is a client-side representation of a `synth` definition

```
//first the Function
{ SinOsc.ar(440, 0, 0.2) }.play;

// now here's an equivalent SynthDef
SynthDef.new("tutorial-SinOsc", { |out| Out.ar(out, SinOsc.ar(440, 0, 0.2)) }).play;
```

Within the function braces, the |out| argument defines a SynthDef control input, which is then used as the first input to Out.ar. It is a good habit to provide an out control in every SynthDef.

### SynthDef with variables

The most common way of creating variables is through putting arguments into the UGen Graph Function. This allows you to set different values when the synth is created. These are passed in an array as the second argument to Synth-new.

```
(
SynthDef("tutorial-args", { arg freq = 440, out = 0;
    Out.ar(out, SinOsc.ar(freq, 0, 0.2));
}).add;
)
x = Synth("tutorial-args");                // no args, so default values
y = Synth("tutorial-args", ["freq", 660]);        // change freq
z = Synth("tutorial-args", ["freq", 880, "out", 1]);    // change freq and output channel
x.free; y.free; z.free;
```

--

## Randomness

https://doc.sccode.org/Guides/Randomness.html

```
// linear distribution in the given range
rrand(24, 48)
```



--

## Busses

Busses come in two types: audio rate and control rate. 
Each one has an index number, starting from 0.

The first two audio busses (index 0 and index 1) will be the outputs, and the two immediately following those (index 2 and index 3) will be the inputs.

The remaining audio busses will be 'private'. These are used simply to send audio and control signals between various synths.

The number of control and audio busses available, as well as the number of input and output channels, is set at the time the server app is booted.


