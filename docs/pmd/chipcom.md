# LFO and Chip-Specific Commands

Now that we've taken a look at the basic sequence, we'll check the chip-specific commands. As this guide is focused on the PC-98 version of PMD, some of the commands present here might not work on the other systems it supports, or might work differently.

## LFO

LFO, or **L**ow **F**requency **O**scillator, is used in PMD for modulating (changing based on a defined pattern) pitch and/or volume. There are many uses for it, but the main ones are vibrato and tremolo effects.

PMD has 2 types of LFO: Software LFO and Hardware LFO.

Hardware LFO is very limited compared to software, so it's unlikely that you'd want to use it, but this limitation also makes it simpler to understand the concepts, so I'll explain it first.

---

### Hardware LFO

The OPN soundchips' internal LFO only affects the FM channels and uses 3 parameters for controlling it: Frequency, Pitch Modulation Level and Amplitude Modulation Level.

**Frequency** sets the speed of the LFO and is shared across all channels. Being a global setting, PMD also uses it in the same command used to activate the Hardware LFO on the song: **`#<value>,<value>`**.

Note: the `#` here is not the same as that used for header commands. The difference is that the latter is *always* is the first non-whitespace character of the line.

In the first value, 1 activates the LFO, and 0 disables it. The second value ranges from 0–7 and controls its frequency. The actual frequency used is specific to the soundchip. Lower values correspond to a lower frequency.

```
A  @0 #1,7 r r       ;Activates hardware LFO and sets its frequency to 7
B  @0 r #1,3 r       ;Changes the frequency value to 3, also affecting channel A 
```

**Pitch Modulation Level** and **Amplitude Modulation Level** are set individually in each channel using the command **`H<value>,<value>`**, where the first value sets the Pitch Modulation Level, and the second, Amplitude Modulation.

Pitch Modulation makes a vibrato-like effect, where its range is increased the higher the value is. The value range is 0–7, where 0 disables it.
```
A   @0 #1,1 H7,0 o5 l4 cdefg
```
Amplitude Modulation's effect depends on the instrument having the AMS parameter set to 1 (see [FM instrument definitions](./structure.md#fm-instruments)). The value range on the H command is 0–3, where 0 disables it. It may also be omitted, defaulting to 0.

The effect it produces depends whether the slots of the instrument which have AMS enabled are *carriers* or *modulators*. A *modulator* slot (often called "operator" in this context) is a slot which modulates a slot ahead of it in the instrument's selected algorithm. A *carrier* operator is a slot which is at the end of the instrument's selected algorithm, thus outputting sound.

The gist of it is that when active in carrier operators, it'll make a tremolo-like effect:
```
@0 4 7                  ;On algorithm 4, OP1 and OP3 are modulators and OP2 and OP4 are carriers 
 31 1 1 1  5 18 0 1 2 0 ;<- AMS is the last parameter in each operator
 31 4 3 8 10  0 0 1 3 1 ;Turns on AMS on OP2 (carrier)
 31 1 1 1  5 10 0 1 6 0
 31 4 3 8 10  4 0 1 5 1 ;Turns on AMS on OP4 (carrier)

A   @0 #1,3 H0,3 o4 l4 cdefg  ;Results in tremolo Effect
```

And on modulators, it'll make a wahwah-like effect:
```
@1 4 7                  ;Same instrument as above, but with different AMS
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

While #0 can be used for disabling the LFO, it'll do so on all channels, so it's advised to use H0 instead if working with a song using Hardware LFO.

---

### Software LFO

Software LFO opens the range of possibilities quite a bit. It works on PCM and SSG, has a wider range of achiavable speeds and waveforms, the commands are fully independent on each channel, and you can even set 2 different LFOs simultaneously for each.

That also means there are more variables to keep in mind:

The 2 central commands for LFO are `M`, which sets the speed and range, and `*`, which sets its activation.

---

### LFO Activation (`*<value>`)

This command sets both when the LFO will activate or deactivate, as well as what it will do, depending on the value.
First, the base values to set what it will do are:
```
0 = LFO is turned Off
1 = Pitch Modulation
2 = Amplitude Modulation
3 = Both Pitch and Amplitude modulation
```
With these base values, the LFO state will be reset for every new note. If you don't want it reset for every note, add 4 (4, 5, 6, or 7).

In the FM channels, the amplitude modulation by default will only affect the carriers for a tremolo effect. If you want it to affect the modulators, you'll have to set it with the LFO operator flag command. See explanation for Amplitude Modulation Level effect in [Hardware LFO](#hardware-lfo) for an explanation about "carrier" and "modulator".

---

### LFO Setting (`M<delay>,<speed>,<depthA>,<depthB>`)

`<delay>` sets how many ticks to wait before the effect starts,`<speed>` sets the tick length for each step, and `<depthA>` sets the amount to vary for each step. How `<depthB>` behaves will depend on the waveform selected.

For this example I'll use the default Triangle waveform:
![Triangle Wavefrom](./images/MW1.png)
_Graphics by Altiami_

> _**An important warning from Altiami**: This graphic is partially incorrect! The height of the wave is `<depthA> * <depthB>`, not `<depthB>`! This graphic will be corrected hopefully sooner than later._

1. Wait for `<delay>` ticks
2. Then change by `<depthA>` every `<speed>` ticks, a total of `<depthB>` times.
3. Inverts the `<depthA>` value to reach the base again
4. Does steps 2 and 3 again, now in the opposite direction because `depthA` is still inverted.
5. Repeats the process until the next reset.

This can be a bit hard to wrap your head around, but what you need to understand is that in this case, `<speed>` and `<depthB>` are what you'll use to set the frequency of the LFO, and `<depthA>` is what you'll use to set the amount to change for each time step.

Combining it with the activation command for a vibrato effect, we'll have it working:
```
G	o5 M24,1,3,2 *1 d1
```

---

### LFO Operator Flag (`MM<operator>`)

This sets which operators will be affected by the LFO. This allows for amplitude modulation on the modulators, as well as pitch modulation on specific operators of FM channel 3.

For the `<operator>`, `1` = Operator 1, `2` = Operator 2, `4` = Operator 3, and `8` = Operator 4. To affect 2 or more operators at the same time, you add the values for the ones you want.

```
A	@1 M0,1,6,4 *2 o3 d1 ; By default it only affects the carriers.
A   MM5 d1               ; Operators 1 and 3 are affected (1+4 = 5)
```

---

### LFO Waveform (`MW<value>`)

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
Each one behaves differently by changing how the values in the LFO setting are calculated. An in-depth explanation about each will be added in the [Advanced info](advanced.md) page in a future update.

---

### Pitch Slide (`MP<value>`)

This a shortcut command made specifically for a pitch slide down (negative value) or pitch slide up (positive value) effect, for which only the `<depthA>` value has to be specified:
```
G   MP-80 *1 o5 c       ;Expands to M0,1,-80,255 *1
```
You can input a delay with a second value too.
```
G   MP20,6 *1 o5 c      ;Expands to M6,1,20,255 *1
```

As such, it replaces the current LFO settings and can be turned off with `*0`.

---

### Multiple LFO Definitions (`<command>[A]`, `<command>B`)

As said above, you can set **2 different LFO settings per channel**. You can specify the second one by adding a `B` as a suffix to the LFO commands.

For visual aid, you can also add an `A` for specifically setting the first one, but it's not needed.
```
A   @1 MA0,1,6,3 *A2 MMA5 ;Wahwah effect on LFO A
A   MPB-6                 ;Pitch down effect on LFO B
A   o3 d1                 ;Both play at the same time
```

---

### Using TimerA for LFO

By default, PMD uses TimerB for its LFO setting, which is controlled by the #Tempo setting or `t` command.

This allows for the LFO to be more easily consistent with the timing of the song, but on the other, hand it also can make the effect timing itself become inconsistent when a song has tempo changes throughout.

Because of this you can choose to use TimerA, which is a fixed clock running at around 54.25 Hz. This is not exact and can vary slightly.

To use TimerA, you either add the following header:
```
#LFOSpeed   Extend
```
Or use the command `MX1`. It can also be disabled with `MX0`.

The header affects all channels, while the command is per channel.

---

## Chip Commands

### Panning (`p<value>`)
Only supported by the FM and PCM channels, this command works the same way in both.

Values are: `1` = Right, `2` = Left, `3` = Both
```
ABC l1 v10 o5 @0  
A   p3 c            ;center pan
B   p1 e            ;right pan
C   p2 g            ;left pan
```
Being a limitation of the soundchip, only hard panning is supported by this command. That is, either the left or right output is completely on, or completely off.

Additionally, only for the PC-98-86 soundboard, the PCM channels support a fourth option. Due to its limited usage, it will be discussed in the [Handling PCM](pcm.md) page.

---

### Chip Part Volume Mixer (`DF<value>`, `DS<value>`, `DP<value>`, `DR<value>`)

This changes the volume of the soundboard's mixer for each part of the YM2608 soundchip. `DF` for FM, `DS` for SSG, `DP` for ADPCM, `DR` for Rhythm. This affects all channels in each part.

The value sets the attenuation level, meaning the volume will decrease as the value increases.
```
G  v15 q2 DS00 d4 DS20 d4 DS40 d4 ;Volume decreases with higher values
```
Use this in conjunction with the volume effects for mixing the song.

This works the same as the `#Volumedown` header.

---

## FM Commands

### Operator Flag (`s<operator>`)

This changes which FM operators the current channel will use. The values for each operator are:

| Slot | Flag |
|------------|---|
| Operator 1 | 1 |
| Operator 2 | 2 |
| Operator 3 | 4 |
| Operator 4 | 8 |

To control 2 or more with the same command, you add the corresponding numbers, so for example, to control Operators 2 and 4, it's 8 + 2 = 10. This is because this flag is done using binary values, allowing them to be stored in a single hex number. **This applies to every command using the `<operator>` flag.**

This command is mainly used for the FM3Extend mode, but can also find its use in other situations.

```
@1 4 7 31 1 1 1 5 18 0 1 2 1 31 4 3 8 10 0 0 1 3 0 31 1 1 1 5 10 0 1 6 1 31 4 3 8 10  4 0 1 5 0 

A   @1 s15 cdefg s12 gdefg   ;Channel plays the full instrument, then only operators 3+4
```

When using this command it's important to **be mindful of the instrument's algorithm** in the instrument being used, as it might lead to the instrument being muted if you carrier operators aren't included.

To disable this command you need to use `s15`. Using `s0` will disable all the operators.

---

### Change Operator Level (`O<operator>,<value>`)

Changes the **total level** (TL) or attenuation level on the selected operator(s) to the value specified. This is specially useful for making cool effects with the modulators, [like this song's bass](https://youtu.be/GxDur7step8?t=50), but it can also be used to change the instrument's base volume level when used in the carriers.

```
;TL on Operator 1 is 18
@1 4 7 31 1 1 1 5 18 0 1 2 1 31 4 3 8 10 0 0 1 3 0 31 1 1 1 5 10 0 1 6 1 31 4 3 8 10  4 0 1 5 0 

A   @1 cdefg O1,25 cdefg       ;Operator 1's TL changes to 30
```

You can also do a relative change by adding a `+` or a `-` before the value.

```
;TL on Operator 1 is 30
@1 4 7 31 1 1 1 5 30 0 1 2 1 31 4 3 8 10 0 0 1 3 0 31 1 1 1 5 10 0 1 6 1 31 4 3 8 10  4 0 1 5 0 

A	@1 Q3 o2 l16
A	[e O5,-1]12 eeee [e O5,+1]12 eeee ;TL on Operators 1 and 3 decreases then increases
```

Changing the instrument with `@` will overwrite this change, and loading the same instrument again it's how you reset the effect without having to type the precise TL value again.

---

### Set Feedback Level (`FB<value>`)

This changes the feedback parameter (FB) on the currently playing instrument.

```
;Feedback in the instrument is 7 (third value)
@1 4 7 31 1 1 1 5 18 0 1 2 1 31 4 3 8 10 0 0 1 3 0 31 1 1 1 5 10 0 1 6 1 31 4 3 8 10 4 0 1 5 0 

A   @1 cdefg FB3 cdefg      ;Changes it to 3
```

Changing the instrument with `@` will overwrite this change, and loading the same instrument again it's how you reset the effect without having to type the precise TL value again.

### Delay Operator Input (`sk<operator>,<value>`)

Delays the input (key-on) in the selected operator(s) by the specified number of ticks:

```
@0 6 6 31 12 9 0 3 43 1 2 0 0 31 9 8 6 3 0 1 1 3 0 31 13 8 5 3 0 1 4 7 0 31 16 16 5 13 13 1 15 0 0

A	@0 l4 o6 cdefg sk12,3 cdefg  ;Higher pitched layer gets delayed a bit
```

This can be used to create subtle effects in combination with instrument definitions, from simple echos, to complex subtone depth (think low-layer reverb, or resonance of bar instruments).

You can turn it off with `sk0`

---

## Volume Envelopes

PMD has 2 types of software envelopes which can be used for both the SSG and PCM channels. Both use the same command but are differentiated by the amount of values input.

### Volume Envelope Setting 1 ( `E<ar>,<dr>,<sr>,<rr>,<sl>,<al>` )

This envelope is ADSR-based, similar to the envelope parameters of the FM instruments, but not necessarily a one-to-one recreation of their speeds.

Parameters and value ranges are:

```
AR = Attack Rate    (0–31)
DR = Decay Rate     (0–31)
SR = Sustain Rate   (0–31)
RR = Release Rate   (0–15)
SL = Sustain Level  (0–15)
AL = Attack Level   (0–15)
```

Attack level is the volume where the attack starts. It can be ommitted, in which case it starts from zero.

```
G   o5 v15 E18,31,15,0,0,0 e1  ;quick fade in then fade out envelope
```

---

### Volume Envelope Setting 2 ( `E<al>,<dd>,<sr>,<rr>` )

This envelope mode is meant to provide a simpler form that is sufficient for most cases.

The parameters indicate:
```
AL = Attack Length   (0–255)
DD = Decay Depth    (-15–15)
SR = Sustain Rate   (0–255)
RR = Release Rate   (0–255)
```

By default, AL, SR, and RR are measured in ticks. See below about using TimerA for exceptions.

The envelope works following these steps:

1. At note start, set the instrument's volume to the volume set by volume commands (such as `v` or `V`).
2. Wait the amount of time specified by AL.
3. Change the volume by the amount specified by DD.
4. Wait the amount of time specified by the SR.
5. Decrease the volume by 1.
6. Repeat steps 4 and 5 until the note is released, at which point, skip to step 7.
7. Wait the amount of time specified by the RR.
8. Decrease the volume by 1.
9. Repeat steps 7 and 8 until the volume is 0 or a new note is played.

If SR is 0, then steps 4 and 5 are skipped, and step 6 only waits to continue upon the note's release.

If RR is 0, then steps 8 and 9 are skipped, and the volume is instantly set to 0.
```
G   o5 v13 E1,-2,2,1 e%6 r4    ;recall that the % means the note length is in ticks
```
In the example above, assuming TimerA is not used, the volume over time would be:
```
13 11 11 10 10 9 (note release) 9 8 7 6 5 4 3 2 1 0
```
Note that the volume starts at the volume of the volume command, `v13`, changes by DD after AL ticks (from 13 to 11), drops by 1 every SR ticks, then by 1 every RR ticks after the note's release. 

---

### Using TimerA for Volume envelopes

By default, PMD uses TimerB for its envelope setting, which is controlled by the `#Tempo` setting or `t` command.

This allows for the envelope to be more easily consistent with the timing of the song, but on the other hand, it also can make the effect timing itself become inconsistent when a song has tempo changes throughout.

Because of this you can choose to use TimerA, which is a fixed clock running at around 54.25 Hz. This is not exact and can vary slightly.

To do so in all channels, you either add the following header:
```
#EnvelopeSpeed   Extend
```
Alternatively you can use the command `EX1` to anable and `EX0` to disable. Using the command instead of the header will only affect the current channel.

## SSG Commands

As mentioned in the Structure page, SSG instruments are made using commands, which are all listed here.

---

### Preset Volume Envelopes (`@<value>`)

PMD has 10 preset envelopes for the SSG, they are selected with the `@` command and the corresponding envelopes are:
```
@0	E0,0,0,0	; Default
@1	E2,-1,0,1	; Synth type 1
@2	E2,-2,0,1	; Synth type 2
@3	E2,-2,0,8	; Synth type 3
@4	E2,-1,24,1	; Piano type 1
@5	E2,-2,24,1	; Piano type 2
@6	E2,-2,4,1	; Glockenspiel/Marimba type
@7	E2,1,0,1	; Strings Type
@8	E1,2,0,1	; Brass type 1
@9	E1,2,24,1	; Brass type 2
```

These presets can't be called on the PCM channels, but you can use the `E` commands above if desired. However, **this is not recommended** due to the volume range differences between SSG and PCM channels.

---

### Channel Mode (`P<value>`)

Sets the current channel to output:
```
1 - Square Wave
2 - Noise
3 - Square + Noise
0 - Mute
```

```
G   @6 v15 l2 P1 c P2 c P3 c P0 c  ;Each of the modes, respectively
```

Note: PMD doesn't support the Envelope Generator natively. Brute-forcing it is possible, but requires extensive knowledge of the soundchip's registers.

---

### Noise Frequency (`w<value>`)

Changes the noise frequency, ranging from 0–31, where the lower the value, the higher the frequency. This affects all SSG Channels.

```
G   @6 v15
G   P2 w0 c w10 c w20 c w30 c   ;Different frequencies in noise mode
G   P3 w0 c w10 c w20 c w30 c   ;And tone+noise mode
```

---

## PCM Commands

As using PCM requires specific setup where each will have its specific additional commands, it's not worth addressing its commands in this page. Check out the [Handling PCM](pcm.md) page for more details.

---