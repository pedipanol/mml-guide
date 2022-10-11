# PMD's MML Structure

PMD is a pretty versatile driver when it comes to its structure. The function of every line is defined in its beginning so you can order things in any way you want. This helps a lot in mantaining the script readable.

He only thing you need for compilation to be successful is a sequence, though the abscence of the parts will result in silence or a error if you address something that hasn't been defined.

Commentaries can be done written on the MML with  `;` at any point, everything after it or until another `;` will be ignored.

---

## Header

Hader lines sets things like metadata and song-wide definitions that affect all channels. They  begin with a `#` and after specifying the instruction, a space or tab is needed before the string or value.

Here I'll explain the most important ones. I'll save some for the specific section they're relevant, and the others can be checked in the manual.

### Metadata:

To include credits/information about the song in the file, we use the following headers:
```
#Title		<string>
#Composer	<string>
#Arranger	<string>
#Memo		<string>
```
`#Title`, `#Composer` and `#Arranger` are straightforward enough.

`#Memo` can contain anything you want the person playing the file to read. You can make multiple `#Memo` lines if desired, but in most players only the last one willl be displayed.

>Note: If you're using the DOS setup to compile and you use japanese/chinese characters in those headers, it's important to set the encoding of your MML file to Shift-JIS, because UTF-8 will output wrong characters after the compilation. In Notepad++ this can be changed in Encoding -> Character Sets -> Japanese -> Shift-JIS.

Example:
>```
>#Title		PMD Test
>#Composer	pedipanol
>#Arranger	pedipanol
>#Memo		I like trains :D
>```

---

### Global Commands

These commands will set things that affect the whole song. Most of these expand to their equivalent commands in compilation.
```
#Tempo		<value>
#Volumedown	<string>
#Transpose	<value>
```

**\#Tempo \<value>** determines the songs BPM... kinda. Due to how the driver was developed, it uses a half-note as a basis instead of a quarter note, so one needs to divide the desired BPM by 2.

```
#Tempo      90  ;The output BPM will be double, 180
```

There's a lot on how PMD's tempo works and alternate methods of defining it, read the Advanced page for more info.

**\#Volumedown \<string>** controls the soundboard's mixer between each group of channels. As it the name implies it only lowers the volume. The format of the string is: \<part>\<value>.

In \<part>, F represents the FM channels, S = SSG channels, P = PCM channels, R = Rhythm channels. \<value> is the amount to lower. When defining multiple volumedowns, use commas between each definition.
```
#Volumedown	F18,S10,P20,R35
```

**\#Transpose \<value>** will transpose all channels (except Rhythm) by the positive or negative amount of semitones you input, by adding a channel transposition command at the beginning of every channel.
```
#Tranpose	-3		;This will transpose the whole song down by 3 semitones
```

---

### Compile Options:
These are instructions that act on compile time and tell it how to handle some things.
```
#Option		<flags>
#Filename	<string>
#Octave		Reverse
```
**\#Option \<string>** lets you which flags to use in the compiler automatically. The commands are the same as the ones indicated by MC.EXE. They can be lower or upper case.

The main flags  you're gonna  for are /V and /C. /V will make sure everything in the MML is properly addressed and /C will display channel lengths, which can help spot possible mistakes.

If using PMDDotNet, a PC98 emulator or a PC98 machine, you can also use /P so the song plays automatically after compiling.

The other options are irrelevant to this guide. But you can view them by running the compiler without addressing an MML file.

```
#Option		/v/c
```

**\#Filename \<string>** lets you determine the name of the compiled file and/or its extension. If this is not included it'll default to \<mmlname>.M .

```
#Filename	SONG.M
```
If you only wish to change the file extension you can only put the extension in there. As said in the file playback section, the extension doesn't change in any way the contents of the file, but it's useful to indicate what setup the song is for. The most common ones are:
```
#Filename	.M		;for OPN/YM2203 (focused) songs.
#Filename	.M2		;for OPNA/YM2608 only songs.
#Filename	.MZ		;for songs using PMDPPZ.
#Filename	.M86	;for songs using 86PCM.
```
**\#Octave Reverse** is a command for reversing the function of the `>` and `<` command. By default, `>` increases and `<` decreases the current octave, This header will reverse it if you prefer the other way around. You can also set it to "Normal" but it's redundant.

---

There are many more headers that can be defined, but the others are more situational, so for the purposes of this guide I'll explain them when each one is relevant. But you can always check the [official manual](https://pigu-a.github.io/pmddocs/pmdmml.htm) and have everything explained together.

All the headers presented above are completely optional for compilation, so you only need the ones you deem necessary for the song you're working on. I only recommend always .having `#Option /v/c` just in case

---

## Instrument Definition

If you don't know by now, the PC-98, with the Speakboard, 86 and other soundboards, uses the [YM2608 or OPNA Soundchip](https://en.wikipedia.org/wiki/Yamaha_YM2608), consisting of:
 - 6 FM Channels (4-operator OPN-type FM Synthesis)
 - 3 SSG Channels ([AYM](https://en.wikipedia.org/wiki/General_Instrument_AY-3-8910)-type PSG)
 - 1 ADPCM Channel (8-bit, max 16khz sample rate)
 - 6 Rhythm Channels (Hardcoded drum samples)

The 86 soundboard also includes its own cpu-powered PCM output. Which is stereo, 16-bit and 44100hz. PMD can harness its power with the PPZ8 driver (for multi-PCM) and PMD86, using it instead of the OPNA's ADPCM.

PMD only has instrument definitions for the FM instruments. They're a string of FM parameter values  beginning with @\<instrument number.

>If you don't know how about FM synthesis in the OPN soundchips it's better to do some research to properly understand how the instruments work and some terms I won't explain. I recommend [smspower's article on the YM2612](https://www.smspower.org/maxim/Documents/YM2612).


### FM instruments
OPN instruments which are accepted by default will need 42 parameter values, which are organized in the following format:

```
; nm alg fbl
@001 004 005
; ar  dr  sr  rr  sl  tl  ks  ml  dt ams
 031 001 001 001 005 026 000 001 002 000
 031 004 003 008 010 000 000 000 003 000
 031 001 001 001 005 020 000 001 006 000
 031 004 003 008 010 004 000 000 005 000
```

They can be organized in any way with including leading zeroes, spacing, line breaks and commas as long as the order is the same and the amount of values and values themselves correct.

To save lines for example, one could organize the same instrument in a single line, like:
```
@1 4 5 31 1 1 1 5 26 0 1 2 0 31 4 3 8 10 0 0 0 3 0 31 1 1 1 5 20 0 1 6 0 31 4 3 8 10 4 0 0 5 0
```
The only problem with doing it this way is that it makes it harder to edit the instrument, if desired.


### OPM instruments

OPM instruments have one additional parameter in each operator, making it 46 in total. You can use them in your MML for the PC98, but you need to use the header **\#DT2Flag	on**, which will tell the compiler to ignore the extra 4 DT2 parameters.
```
#DT2Flag on

; nm alg fbl
@001 004 005
; ar  dr  sr  rr  sl  tl  ks  ml  dt dt2 ams
 031 001 001 001 005 026 000 001 002 000 000
 031 004 003 008 010 000 000 000 003 000 000
 031 001 001 001 005 020 000 001 006 000 000
 031 004 003 008 010 004 000 000 005 000 000
```
This is the same instrument as before but now in the OPM format.
>Note: that this makes it so every instrument needs to be in OPM mode, so it's better to remove the DT2 parameter if all the others are in OPN mode.

### Using an external FM instrument file

If using instruments from a preset bank, or if you don't want to include the FM instruments in your MML for whatever reason, you might want to use an instrument file instead.

.FF are PMD's instrument bank files, which will be referenced by the script when loading instruments with the `@` commands. They can be made with VEDSE.EXE on DOS or YM2608 Tone Editor on Windows.

>YM2608 tone editor can be also used to convert commom FM instrument file formats to .FF banks.

To use the bank file in your script, you need to use the following header:

```
#FFFile     <filename>.FF
```

---
### SSG and PCM instruments
SSG and PCM don't have an instrument definition in the same way that FM does.

SSG is shaped by commands that will change its wave mode, volume envelope and noise frequency which are explained in the    chip-specific commands page.

PCM is defined both by external file handling and chip commands, thus needing its own page.

---

## Channels

In PMD, channels are defined at the beginning of each line. This means all the sequence written in the current line will be played in the channel associated with the letter(s) at the beginning.

A space or tab is needed after defining the channel(s).

```
G   l8 cdefgab>c4.   ;Channel G plays the first line
H   l4 c e d g c     ;Channel H plays the second line
```

PMD also allows you to address 2 channels at the same time. This will not necessarily make them play together, since the prior sequence would have to be in sync. One can take advantage of this to create 2+ channel layering or echo.

```
G   v12              ;Sets Channel G
H   r8. D-2 v8       ;Sets Ch. H to play with a detune and delay
GH  o4 l8 cdefgab>c  ;Play both, H sounding like an echo of G
```

The channel letter association with the soundchip is as following:
```
A   FM Channel 1
B   FM Channel 2
C   FM Channel 3
D   FM Channel 4
E   FM Channel 5
F   FM Channel 6
G   SSG Channel 1
H   SSG Channel 2
I   SSG Channel 3
J   ADPCM/86PCM Channel
```

The Rhythm channels can be used in any of those channels, as it's called using commands, allowing it to be easily played together with the drums being played in any of these channels.

PMD also has driver channels that perform different functions:
```
K          Rhythm/SSGPCM Channel
R<value>   Rhythm channel macros
```
The K channel is an option for sequencing drums. It works on SSG channel 3 and takes priority over channel I. By default it'll be used for the internal SSG drums (notably used on Touhou games), but with additional setup it can play SSGPCM samples or custom SSG drums or SFX.

Extra channels can be created for using FM channel 3's Expanded mode and PPZ8. You can associate them with any letter you want (besides the default ones) as long as it's defined on the following headers:

```
#FM3Extend  <channel(s)> ; Commonly XYZ
#PPZExtend  <channel(s)> ; Commonly abcdefgh
```

## Macros

Aside from R which can only be used in the K channel, PMD has an universal macro. It works by setting it up in a marco line starting with `!`, associating it with any string, then calling that string in the channel sequence. As follows:

```
!a    cdefg       ;Sets the macro "a" with the "cdefg" sequence.

A   !<string>     ;Inserts "cdefg" in channel A
```

The macros are inserted on compile time, meaning it'll only save typing and time. It's good to use macros whenever you see yourself repeating a sequence or sequence of commands often. They're notably seen in drums.