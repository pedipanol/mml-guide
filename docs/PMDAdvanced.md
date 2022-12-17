# Advanced info

If you've read the guide up to this point, congrats! This page is for the more advanced stuff which you don't need to know for most use-cases of the driver, but might be a good piece of knowledge to have.

## Tempo, TimerB and Whole Note Length

In the info about the `#Tempo` header and `t<value>` command I mentioned that due to how PMD's tempo was calculated, by default the tempo setting needs to be half of the desired BPM of the song. Let's break down why.

There are 2 factors that determine the speed of the song, the TimerB and the Whole Note Length.

### TimerB

TimerB is the variable clock of the OPNA, which will run cycles, or tick, at the determined speed, and PMD will use that to trigger its events, including the sequence. 

We can set the timer with the following header, range is 1-255:

```
#Timer  192
```

Or with an upper-case `T<value>` command:

```
G   cgfe T200 cgfe
```

It can also changed relatively by adding a +/- sign before the number.

### Whole Note Length

Whole Note Length refers to how many ticks a whole note, or `l1` note length, will contain. PMD defaults it to 96 as it's a round number that allows for a wide range of divisions, which is how the note lengths are calculated. So:

`l1 = 96 ticks`, `l2 = 48`, `l4 = 24`, `l8 = 12`, `l16 = 6`, `l32 = 3`, etc.`

However we can change that default length value using the following header, range is 1-255:

```
#Zenlen     <value>
```

>Trivia: The "Zen" in Zenlen stands for 全音符(zen onbu), literally meaning whole note.

Or the `C<value` command, allowing it to be changed mid-song.

```
G   c C192 g  ;Changes the Whole Note Length from 96 (default) to 192
```

Since PMD's note length calculation is based off that number and it doesn't do any form of rounding, if you input a note length value that won't result in an integral, you'll get in a compilation error

```
#Zenlen     90
G     c16       ;90 can't be divided by 16, compilation fails.
```

### Tempo

As said in their explanations previously, the `#Tempo` header or `t<value>` command  will change TimerB to match how many times a 48 tick cycle will repeat in a minute approximately. By default this makes it so the value has to be half the desired bpm in the default settings. 

While this is meant to make it simpler, and it does achieve that somehow, it makes difficult to get a precise timing for the tempo. Because TimerB's speed is logarithmic, limiting its precision.

### Calculating the TimerB

If you desire a precise bpm for the song, you can use the following equation to get the TimerB value from the desired `bpm` and the Zenlen you're using.

```
TimerB = 256 - [10800000 / (bpm * 13 * Zenlen)]
```

If you get a decimal output which would need you to round it down, change the Zenlen values (keep in mind the division limitation) and try finding one that gets you the desired result or a better approximation of it.

## Advanced Commands

Some commands that are too specific or difficult to use for the overview guide. Not an exhaustive list for now as some of them require things I'll cover in the future, so I'll save it for then.

### Hexadecimal Value Definition

Something I refrained from saying is that you can input values in Hex instead of Decimal, which works for almost all commands.

To do so, simply add a `$` symbol before the command:

```
A   @1 O1,$20 c
```

### Direct Register Writes (`y<register>,<value>`)

This command will directly write into the OPNA registers. Which the recommended use case is allowing for doing things the driver doesn't natively support.
```
A   @0 l8 y$30,0 c y$30,1 c y$30,2 c y$30,3 c y$30,4 c 
; Changes register $30's (This channel's Operator 1's MULT)
```
However it's a setting that requires you to have extensive knowledge of the soundchip's register map, and PMD can overwrite many of the changes when using other commands, or not at all, resulting in a broken song that requires a hardware or register reset in order to work properly again.
