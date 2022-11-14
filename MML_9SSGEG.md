# SSG-EG

SSG-EG is a feature of the OPN that replaces the ADSR envelope with a wave shape. It works similar to an amplitude LFO, except it can get to frequencies high enough to be perceived as a note pitch, and modify the base sine wave. This allows for creating more complex sounds with the FM channels.

However it`s a pretty complex feature that not even many musicians used before the 2000's. Because of this not many players and emulators support it, it wasn't even implemented into PMD properly until KAJA updated it in 2020.

## Players

First you must ensure the player you're using supports it.

| Player | SSG-EG | Comment |
| ------ | ------ | ------- |
| FMPMD2000 | Yes | Versions released after 2020 |
| 98fmplayer | No | |
| PMDDotNET | No | Dev plans to add it |
| MML2VGM | No | Dev plans to add it |
| NekoProject II fmgen | Yes | use genpfm.dll |
| DOSBox-X | No | |

## Usage

SSG-EG is a complicated feature that you have to build your instrument speficically to use, or else it won't work. Using an instrument editor such as YM2608 tone editor on Windows or VEDSE on DOS will allow you to test the values properly.

After creating a instrument and putting it in the script, call the instrument with `@` and then use the command `SE<operator>,<value>`:

```
@0 0 0 31 19 31 15 2 29 0 7 7 0 31 11 31 15 1 28 0 0 3 0 24 11  0 15 2 29 0 2 0 0 27 13 12 15 2  2 0 2 0 0

A	@0 l8 o3 [c4gc2.<g16g16>[c<g]3]4  ;Instrument without SSG-EG
A	SE3,8    [c4gc2.<g16g16>[c<g]3]4  ;Activating SSG-EG changes the sound to a timpani-like sound
```

It's important to note that the SSG-EG won't reset on instrument change, so you need to deactivate it with `SE0` when changing to a instrument that doesn't use it.

If you don't know how SSG-EG works and how to make instruments that use it, [this video](https://youtu.be/IKOR0TUlnWU) has a quick look into it at the first part. The software is different but it works the same.