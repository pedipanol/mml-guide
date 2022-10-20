# LFO and Chip-Specific Commands

Now that we took a look at the basic sequence, we'll check the chip-specific commands. As this guide is focused on the PC-98 version of PMD, some of the commands present here might not work on the other systems it supports, or might work differently.

## LFO

LFO, or Low Frequency Oscillator is used in PMD for modulating pitch and/or volume. There are many uses for it but the main ones are for a Vibrato and Tremolo effect.

PMD has 2 types of LFO: Software LFO and Hardware LFO.

Hardware LFO is very limited compared to software, so it's unlikely that you'd want to use it, but this limitation also makes it simpler to understand the concepts, so I'll explain it first.

---

### Hardware LFO

The OPN soundchips' internal LFO only affects the FM channels and
use 3 parameters for controlling it: Frequency, Pitch Modulation Level and Amplitude Modulation Level.

Frequency sets the speed of the LFO and is shared across all channels. Being a global setting, PMD also uses it in the same command used to activate the Hardware LFO on the song: **#\<value>,\<value>**

In the first value, 1 activates the LFO and 0 disables it. The second value ranges from 0 to 7 and controls its frequency.
```
A  @0 #1,7 r r       ;Activates LFO and sets its frequency to 7
B  @0 r #1,3 r       ;Changes the frequency value to 3, also affecting channel A
```

Pitch Modulation Level and Amplitude Modulation Level are set individually in each channel using the command **H\<value>,\<value>** where the first value sets the Pitch Modulation level and the second, Amplitude Modulation.

Pitch Modulation makes a vibrato-like effect which its range is increased the higher the value is. The value range is 0-7, where 0 disables it.
```
A   @0 #1,1 H7,0 o5 l4 cdefg
```
Amplitude Modulation's effect depends on the instrument having the AMS parameter set to 1. The value range on the H command is 0-3, where 0 disables it.

The gist of it is that when active in carrier operators it'll make a tremolo-like effect:
```
@0 4 7 ;On algorithm 4, OP1 and OP3 are modulators and OP2 and OP4 are carriers 
 31 1 1 1  5 18 0 1 2 0 ;<- AMS is the last parameter in each operator
 31 4 3 8 10  0 0 1 3 1 ;Turns on AMS on OP2 (carrier)
 31 1 1 1  5 10 0 1 6 0
 31 4 3 8 10  4 0 1 5 1 ;Turns on AMS on OP4 (carrier)

A   @0 #1,3 H0,3 o4 l4 cdefg  ;Results in tremolo Effect
```

And on modulators it'll make a wahwah-like effect:
```
@1 4 7 ;Same instrument as above, but with different AMS
 31 1 1 1  5 18 0 1 2 1 ;Turns on AMS on OP1 (modulator) 
 31 4 3 8 10  0 0 1 3 0 
 31 1 1 1  5 10 0 1 6 1 ;Turns on AMS on OP3 (modulator)
 31 4 3 8 10  4 0 1 5 0 

A   @1 #1,3 H0,3 o4 l4 cdefg  ;Results in wahwah effect
```
You can enable both Pitch Modulation and Amplitude modulation at the same time.

A third value can be added to the `H` command, which will make it delay its activation by the set number of ticks:
```
A   @0 #1,1 H7,0,24 o5 l2 cdefg
```

While #0 can be used for disabling the LFO, it'll do so on all channels, so it's advised to use H0 instead if working in a song using Hardware LFO.

---

### Software LFO

Software LFO opens the range of possibilities quite a bit. It works on PCM and SSG, has a wider range of achiavable speeds and waveforms, the commands are fully independent on every channel, and you can even set 2 different LFOs simultaneously in each.

That also means there are more variables to keep in mind:

The 2 central commands for LFO are `M`, which sets the speed and range, and `*`, which sets its activation.

### LFO Activation *\<value>

This command sets both when a command will activate or deactivate as well as what will it do depending on the value:
```
0 = LFO is turned Off
1 = Pitch Modulation
2 = Amplitude Modulation
3 = Both Pitch and Amplitude modulation
```
With these values, the LFO state will be reset on every new note. If you don't want it reset in everynote, use 4 5 6 and 7 respectively instead.

In the FM channels, the amplitude modulation by default will only affect the carriers for a tremolo effect. If you want it to affect the modulators you'll have to set it with the LFO operator flag command.

### LFO Setting ( M\<delay>,\<speed>,\<depthA>,\<depthB> )

\<delay> sets how many ticks to wait before the effect starts, \<speed> sets the tick length for each step, and \<depthA> sets the amount to vary in each. How \<depthB> behaves will depend on the waveform selected.

For this example I'll use the default Triangle waveform:
![Triangle Wavefrom](/images/MW1.png)
_Graphics by Altiami_

After \<delay> ticks, the LFO will take \<depthA> steps every \<speed> ticks, \<depthB> times. Then it inverts the \<depthB> value to reach the base again and then do the same on the opposite direction.

This can be a bit hard to wrap your head around, but what you need to understand is that in this case \<speed> and \<depthB> is what you'll use to set the frequency of the LFO, and \<depthA> is what you'll use to set the amount to change.

Combining it with the activation command for a vibrato effect:
```
G	o5 M24,1,3,2 *1 d1
```

---

### Operator Flag ( MM\<value> )

This sets which operators will be affected by the LFO. This allows for amplitude modulation on the modulators as well as pitch modulation on specific operators of FM channel 3.

For the value, Operator 1 = 1, Operator 2 = 2, Operator 3 = 4, and Operator 4 = 8.
To affect 2 or more operators at the same time, you add the values for the ones you want.

```
A	@1 M0,1,6,4 *2 o3 d1 ; By default it only affects the carriers.
A   MM5 d1               ; Operators 1 and 3 are affected (1+4 = 5)
```

---

### LFO Waveform (MW\<value>)

This changes the waveform used with the corresponding value.
```
0 Triangle Wave 1 (default)
1 Sawtooth Wave
2 Square Wave
3 Random Wave
4 Triangle Wave 2
5 Triangle Wave 3
6 One-shot
```
Each one behaves differently by changing how the values in the LFO setting is calculated. For a in-depth explanation check out the Advanced Section of the guide.

---

### Pitch Slide ( MP\<value> )

This a shortcut command made specifically for a pitch down (negative value) or pitch up (positive value) effect. On which only the depthA value has to be specified:
```
G   MP-80 *1 o5 c       ;Expands to M0,1,-80,255 *1
```
You can input a delay with a second value too.
```
G   MP20,6 *1 o5 c      ;Expands to M6,1,20,255 *1
```

As such, it replaces the current LFO settings and can be turned off with *0.

---

### Multiple LFO Definition ( \<command>A , \<command>B )

As said above, you can set 2 different LFO settings per channel. You can specify a second one by adding a `B` between any of the commands in this section and their values.

For visual aid you can also add an `A` for setting specifically the first one but it's not needed.
```
A   @1 MA0,1,6,3 *A2 MMA5 ;Wahwah effect on LFO A
A   MPB-6                 ;Pitch down effect on LFO B
A   o3 d1                 ;Both play at the same time
```

---

## Chip Commands

### Chip Part Volume Mixer ( DF\<value> , DS\<value> , DP\<value> , DR\<value> )

This changes the volume of the soundboard's mixer for each part of the YM2608 soundchip. DF for FM, DS for SSG, DP for ADPCM, DR for Rhythm. This affects all channels in each part.

The value sets the attenuation level, meaning the volume will decrease as the value increases.
```
G  v15 q2 DS00 d4 DS20 d4 DS40 d4 ;Volume decreases with higher values
```
Use this in conjuction with the volume effects for mixing the song.

This works the same as the `#Volumedown` header.