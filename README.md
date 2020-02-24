# goertzel-stream

> Detects the presence of a single frequency in a stream of signal samples.

(Attention: this is a fork that was modified to use stream Buffers instead of AudioBuffers)

# example

```js
var goertzel = require('goertzel-stream')
var ToneStream = require('tone-stream')

var freq = 697

var ts = new ToneStream({
	sampleRate: 44100,
	bitDepth: 16,
	channels: 1
})

ts.add([1000, freq])
ts.add([1000, 0]) // silence

// Detection stream looking for the 697 Hz frequency
var detect = goertzel(freq)

// Pipe the signal into the detector
ts.pipe(detect)

detect.on('toneStart', function (tones) {
  console.log('start', tones)
})
detect.on('toneEnd', function (tones) {
  console.log('start', tones)
})
```

```
start { '697': { start: 0 } }
start { '697': { start: 0, end: 0.03 } }
```

# api

## var detect = goertzel(freq, opts={})

Returns a WriteStream set to detect frequencies. `freq` can be a single number
or an array of frequencies to detect.

`opts` is mandatory, and has some required and optional parameters:

- `opts.sampleRate` (required) - how many samples are taken per second. For best
  results, this should be at least twice the [Nyquist
  frequency](https://en.wikipedia.org/wiki/Nyquist_frequency). 2.5x works well.
- `opts.testsPerSecond` (optional) - How many tests for the frequency to perform
  per second's worth of samples. Defaults to 100.

Now you're ready to pipe in an audio source!

## detect.on('toneStart', function (tones) { ... })

Emitted when a tone begins. `tones` is an object mapping a frequency to its
start time.

```
{ '697': { start: 0 } }
```

## detect.on('toneEnd', function (tones) { ... })

Emitted when a detected tone ends. `tones` is an object mapping a frequency to
its start time and end time.

```
{ '697': { start: 0, end: 1 } }
```

# install

With [npm](https://npmjs.org/) installed, run

```
$ npm install goertzel-stream
```

# license

MIT
