# PMD's MML Structure

PMD is a pretty versatile driver when it comes to its structure. The function of every line is defined in its beginning so you can order things in any way you want. This helps a lot in mantaining the script readable.

He only thing you need for compilation to be successful is a sequence, though the abscence of the parts will result in silence or a error if you address something that hasn't been defined.

Commentaries can be done written on the MML with  `;` at any point, everything after it or until another `;` will be ignored.
## Header
Hader lines sets things like metadata and song-wide definitions that affect all channels. They  begin with a `#` and after specifying the instruction, a space or tab is needed before the string or value.

Here I'll explain the most important ones. I'll save some for the specific section they're relevant, and the others can be checked in the manual.

##### Metadata:

To include credits/information about the song in the file, we use the following headers:
```
#Title		<string>
#Composer	<string>
#Arranger	<string>
#Memo		<string>
```
`#Title`, `#Composer` and `#Arranger` are straightforward enough.

`#Memo` can contain anything you want the person playing the file to read. You can make multiple `#Memo` lines if desired, but in most players only the last one willl be displayed.

>Note: If you're using the DOS setup to compile and you use japanese/chinese characters in those headers, it's important to set the encoding of your MML file to Shift-JIS as UTF-8 will output wrong characters after the compilation. In Notepad++ this can be changed in Encoding -> Character Sets -> Japanese -> Shift-JIS.

Example:
```
#Title		PMD Test
#Composer	pedipanol
#Arranger	pedipanol
#Memo		I like trains :D
```

##### Global Commands

These commands will set things that affect the whole song. Most of these expand to their equivalent commands in compilation.
```
#Zenlen		<value>
#Tempo		<value>
#Timer		<value>
#Volumedown	<string>
#Transpose	<value>
```

**\#Zenlen \<value>** determines the tick length of a whole note. By default it's 96 ticks, meaning a halfnote will be 48 ticks, a quarter, 24 ticks and so on. It's important to know it exists, but it's dangerous to change from the default.

PMD will divide this value by each note's length, so if you set it to a value that can't be divided by a length you used, it'll result in a compilation error.

This will also affect how the Tempo and Timer commands affect the speed of the song.

>Trivia: Zenlen is an abbreviation of "全音符(zen onbu) Length", meaning whole note length.

**\#Tempo \<value>** will change TimerB to match how many times a 48 tick cycle will repeat in a minute approximately. By default this makes it so the value has to be half the actual tempo. 

If you set \#Zenlen to 192, this will make the tempo value be closer to the actual tempo, but will also increase the imprecision. Sticking to the default is recommended.

**\#Timer \<value>** will change the speed value of TimerB directly.

>Since  \#Tempo is tied to this 48 tick cycle, if your \#Zenlen is not a multiple of 48 cycles, it's better to use \#Timer instead. And use the following formula
>
>`TimerB = 256 - 10800000 / (bpm * 13 * Zenlen)`

**\#Volumedown \<string>** controls the soundboard's mixer between each group of channels. As it he name implies it only lowers the volume. The format of the string is: \<part>\<value>.

In \<part>, F represents the FM channels, S = SSG channels, P = PCM channels, R = Rhythm channels. \<value> is the amount to lower. When defining multiple volumedowns, use commas between each definition.
```
#Volumedown	F18,S10,P20,R35
```

**\#Transpose \<value>** will transpose all channels (except Rhythm) by the positive or negative amount of semitones you input, by adding a channel transposition command at the beginning of every channel.
```
#Tranpose	-3		;This will transpose the whole song down by 3 semitones
```

##### Compile Options:
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

## Instrument Definition

PMD only has instrument definitions for the FM instruments. They're a string of FM parameter values  beginning with @\<instrument number.

>If you don't know how about FM synthesis in the OPN soundchips it's better to do some research to properly understand how the instruments work and some terms I won't explain. I recommend [smspower's article on the YM2612](https://www.smspower.org/maxim/Documents/YM2612).

### OPNA instruments
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

### External instrument file
If using instruments from a preset bank, you might want to use an .FF file instead of including everything in the MML itself.