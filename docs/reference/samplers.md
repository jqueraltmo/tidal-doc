---
title: Samplers
id: samplers
---

This page presents many functions related to the use of samples inside TidalCycles.

For specific information about functions used to slice and loop samples see [Sampling](https://tidalcycles.org/docs/reference/sampling).

Each function will be presented following the same model:
* **Type signature**: how the function is declared on the **Haskell** side.
* **Description**: verbal description of the function.
* **Examples**: a small list of examples that you can copy/paste in your editor.

## Basic sample manipulation

### amp

```haskell
Type: amp :: Pattern Double -> ControlPattern
```

`amp` is used to control the amplitude (volume) of the sound. It's very similar
to `gain`, but it uses a linear function. Its default value is `0.4`.

```haskell
d1 $ s "arpy" # amp 0.6
```

This will play the first `arpy` sample at a volume slightly louder than the default.

```haskell
d1 $ s "arpy" # amp "<0.4 0.8 0.2>"
```

In the above example, the volume changes at each cycle.

### begin

```haskell
Type: begin :: Pattern Double -> ControlPattern
```

`begin` receives a pattern of numbers from 0 to 1. It skips the beginning of each sample. The numbers indicate the proportion of the samples that needs to be skipped (`0` would play the sample from the start, `1` would skip the whole sample, `0.25` would cut off the first quarter from each sample). For example:

```haskell
d1 $ s "bev" # begin 0.5 # legato 1
```

In the above example, the sample is started from the half of its total length.

```haskell
d1 $ n "0 1 2" # s "ade" # begin "<0 0.25 0.5 0.75>" # legato 1
```

In this other example, the first `3` `ade` samples are playied on every cycle, but the start point from which they are playied changes on each cycle.

### end

```haskell
Type: end :: Pattern Double -> ControlPattern
```

The same as `begin`, but cuts off the end of samples, shortening them. For example, `0.75` will cut off the last quarter of each sample.

```haskell
d1 $ s "bev" # begin 0.5 # end 0.65
```

This will play only a small part of the sample: from `50%` its length to `65%` its length.

```haskell
d1 $ s "bev" >| begin 0.5 >| end "[0.65 0.55]"
```

The example above will play the sample two times for cycle, but the second time will play a shorter segment than the first time, creating some kind of canon effect.

### gain

```haskell
Type: gain :: Pattern Double -> ControlPattern
```

`gain` is used to control the amplitude (volume) of the sound. Values less than `1` make the sound quieter. Values greater than `1` make the sound louder.

`gain` uses a power function, so the volume change around `1` is subtle, but it gets more noticable as it increases or decreases. Typical values for `gain` are between `0` and `1.5`. For the linear equivalent, see `amp`.

```haskell
d1 $ s "arpy" # gain 0.8
```

This plays the first `arpy` sample at a quieter level than the default.

```haskell
d1 $ s "ab*16" # gain (range 0.8 1.3 $ sine)
```

This plays a hihat sound, `16` times per cycle, with a `gain` moving from `0.8` to `1.3` following a sine wave.

### grain

```haskell
Type: grain :: Pattern Double -> Pattern Double -> ControlPattern
```

`grain` is another way to specify what part of samples we want to play. Instead of specifying the `begin` and `end`, here we write the `begin` and the `length`.

For example:

```haskell
d1 $ slow 2 $ s "bev" # grain 0.2 0.1 # legato 1
```

is equivalent to:

```haskell
d1 $ slow 2 $ s "bev" # begin 0.2 # end 0.3 # legato 1
```

### grain'

```haskell
Type: grain' :: Pattern String -> ControlPattern
```

`grain'` is simply a fast shortcut to join a `begin` and an `end`.

```haskell
d1 $ slow 2 $ s "bev" # grain' "0.2:0.3" # legato 1
```

This example is equivalent to:

```haskell
d1 $ slow 2 $ s "bev" # begin 0.2 # end 0.3 # legato 1
 ```

## Sample effects

### accelerate

```haskell
Type: accelerate :: Pattern Double -> ControlPattern
```

A pattern of numbers that speed up (or slow down) samples while they play.

```haskell
d1 $ s "arpy" # accelerate 2
```

In this example, the sound starts at the original pitch, and gets higher as it plays. You can use a negative number to make the sound get lower.

```haskell
d1 $ arp "up" $ note "c'maj'4" # s "arpy" # accelerateTake "susan" [0.2,1,-1]
```

Using [state values](https://tidalcycles.org/docs/reference/state_values/#introduction-to-state-values), in this example we apply a different acceleration to each played note.
