# pedipanol's guide to basic MML syntax
Hello! I'm Saria and here's another attempt to explain MML script to anyone who's willing to learn! This will cover things similar in the most formats, including PMD, (pp)mck, MDXDRV, gbmc, mgsdrv, MML2VGM.

*SNES MML is different from the rest because it was born from romhacking tools instead being conceived as proper MML formats. So things said here might not be applied there.*

## The Basic Structure

In most MML formats there will be 3 essential parts to its structure:
1. The Header
2. Instrument Macros
3. Sequence

Look at this sample MML made for mgsdrv and try to identify them:
```
#opll_mode 1
#tempo 150
#title { "GRAND DAD?!"}

@s00 = { 7F7F7F7F7F7F7F7F8080808080808080 80808080808080808080808080808080 }
@s01 = { 001931475A6A757D7F7D756A5A473119 00E7CFB9A6968B8380838B96A6B9CFE7 }

4 v10
5 v3 r8. \-1
45 @0 o4 l4 b-e-r8>e-c8 <b-e-r8b-a-8 q6 g16g16g8 q8 a-8b-8>e-fg1
6  v12 @1 o4 l16 q7 [e-e-e-e- e-8e-e- e-8e-e- e-8e-8]2 q8 e-2f4b-4c1
```

You can listen to it @ https://f.msxplay.com?id=73761dd0 .

It's laid out literally in the order I mentioned, but the truth is that it doesn't need to. Because how a line starts dertermines its function and what you can do in them. To me, separating them clearly makes it easier to understand, but you can put in whatever order you like.

The header lines are started with **\#**, in there you define all sorts of metadata for the song, that will be output in the compiled file. In some drivers you don't need it for compiling, but in others it may result in an error.

The instrument definitions are started with **\@**, this is the part that varies the most depending on the driver you're using. In the example above, it's defining the wavetable to be used by the SCC, so it takes the 32 values in hexadecimal (it also recognizes decimal if you want but it's more common to find them in hex).

The sequence lines are started with **non-symbol characters**. In here each character refers to a specific channel of the soundchip, so you can combine them to make 2 channels play the same line and things like that, really useful for layering and multi-channel echo. But be careful to not use commands the channels don't share or it'll result in an error.

You can also **comment lines**, in most it's done starting the line with **;** but there are exceptions.

Since the Header and Instrument definitions are driver-specific, I'll save a more detailed explanation for them for my specific guides. Time for explaining the basic sequence!

## The Basic Sequence
Sheet music in text form is the best way to describe MML sequencing, as it holds a lot of similarities to it. If you know how to read it, you're probably have an easier time understanding it! 

One critical thing to understand that the syntax is **almost always case sensitive**! We're using mgsdrv which is an exception to this, but in most others, writing without the proper casing may lead to problems in the song after compiling or straight up not compile at all, so pay attention!

So let's get started! I'll write the melody of the example above again for funsies, though I'll transpose it to make it easier. If you wanna follow along in mgsdrv in your browser, start from [this template!](https://f.msxplay.com?id=e04930ad)! I recommend doing so and pressing play after each example to hear the results!

### 1 - Notes

Starting with the notes, we just type their names in lowercase! So they're `a b c d e f g`, no surprise! I advise humming as you type each note, as it will help your perception!
```
addbadagffgadef
```
To add rests we use `r` ! They will operate the same way as a note!
```
adrdbadragffgadef
```
Since the melody is in D major, we gotta change those Fs into F-sharps. To do that, we type `+` right after the note! You can also make them flat using `-` instead!
```
adrdbadragf+f+gadef+
```
Maybe you've noticed that all the notes are playing in the same octave, when one of them is not supposed to! One way we could change it is by using the `o` command followed by the octave number, but we can just increase or decrease from the current octave, using `>` and `<` respectively!
```
adr>d<badragf+f+gadef+
```

### 2 - Note lengths

You've noticed that all the notes are playing with the same duration, so let's change that! But first, a nice thing about mml is that you're free to space things up as you want in most formats! I like using space to make it easier to read, by separating the notes in each measure.
```
adr>d<b adrag f+f+gade f+
```
To change note lengths we add numbers after the notes, that number is the fraction of a whole note, so 1 = whole note, 2 = half note, 4 = quarter note, and so on.
```
a4d4r8>d4<b8 a4d4r8a4g8 f+8f+8g8a8d4e4 f+1
```
There! We got the down! But... it looks kind of a mess doesn't it?

The reason why the compiler doesn't require a number after is that there's an internal note length being used, by default it's 4, but we can change it using the `l` command followed by the length you want!

I recommend using changing it to the highest number of notes with the same note length you're gonna use. In this case both have the same amount so I just use the one that feels better to me!
```
l4 adr8>d<b8 adr8ag8 f+8f+8g8a8de f+1
```

A side note: same as in sheet music, you can dot the notes using `.`! This will extend the notes by half of its duration. An alternate way of writing this melody without the rests is
```
l4 ad.>d<b8 ad.ag8 f+8f+8g8a8de f+1
```

### 3 - Loops

One last important thing to the sequencing itself are loops. By using loops you save having to type notes repeatedly! To do that we encase the sequence within `[ ]` and after the last bracket we add how many times we want it to play. We want it to play it twice, so let's add a 2!

```
l4 [adr8>d<b8 adr8ag8 f+8f+8g8a8de f+1]2
```
The original repeats the notes, but it changes the last note. To do that we use a loop break, which will be a symbol that varies a lot depending on the driver. In mgsdrv, it's gonna be `|`. This will whatever is after the symbol be skipped to the end of the loop on the last time the it's playing.
```
l4 [adr8>d<b8 adr8ag8 f+8f+8g8a8de |f+1]2 d1
```
And.. that's what we needed for this melody! Pretty much the only thing that will change in this sequence if you want to write it in another driver is the aforementioned look break!

## The Common Commands

Here's a less tutorial-ish explanation on things if that bored you! 
It's a list of commands that are common to most drivers and are essential to sequencing. There will be differences between how each is implemented in each driver, and there'll be exception for the commands.

This is just to give an idea of the main things you'll want for the sequencing

### Set Instrument
Syntax: `@(value)`
Select the instrument definitions to be used. Use the same value as one instrument you have set previously.

<details><summary>Example:</summary>
 
```
@1 cdefg @2 cdefg
```
(audio)
</details>

### Notes
Syntax: `a b c d e f or g (+ or -)`
This plays the note!
Adding a + or - will make it sharp or flat, respectively!

<details><summary>Example:</summary>
 
```
l4 cdefgab>c
```
(audio)
</details>

### Rests
Syntax: `r`
Adds a rest! It operates the same way as a note command.

<details><summary>Example:</summary>
 
```
l8 crcrgrgrararg
```
(audio)
</details>

### Note Length
Syntax: `note(note length value)`
This sets the current note's duration! The value is divided from a whole note. Omitting it will use the default note length instead.

Not all numbers will work, as it depends on the tick count of the whole note, if the divided value isn't an integer, it won't be valid.

By default, the accepted values are: `1, 2, 3, 4, 6, 8, 12, 16, 24, 32, 48` . The multiples of 3 are triplets!

Adding a `.` at the end increases the length by half!

<details><summary>Example:</summary>
 
```
a4d4.>d4<b8 ad.ag8
```
(audio)
</details>

### Relative Octave Change
Sintax: `<` and `>`
Respectively decreases and increases the current octave by 1.
You can use header configs to reverse it.
<details><summary>Example:</summary>
 
```
l16 dd>d<d f8ab >crcr<b4r4
```
(audio)
</details>

### Set Octave
Sintax: `o(octave value)`
This changes the current octave to the specified value.
I recommend using when changing instruments or when you want to have a firm grasp of the current octave, as one can get lost after many relative octave changes.
  
<details><summary>Example:</summary>
 
```
l8 o3 cde o6 cde o2 cde
```
(audio)
</details>

### Ties
Syntax: `^(length value)`
Extends the length of the note by the specified value. You can stack as many as desired.
Some drivers unite this with the Slur command, but it's better to understand them separately, since it can cause errors when using a different driver.
<details><summary>Example:</summary>
 
```
l16 b-4f4^8b-8 b->cde-f2^4^8
```
(audio)
</details>

### Slur
Syntax: `&(note)`
Also known as legato. Plays the next note without restarting the instrument's envelope.
This can also be used to achieve a tie, but it's not advised, since it'll have a bigger filesize compared to Ties.
<details><summary>Example:</summary>
 
```
l16 d&efg ar>d8&cr<arb4
```
(audio)
</details>

### Loop
Syntax: `[ (mml string) (break) ](number of repetitions)`
A loop plays the MML string between the brackets for the amount of times specified after.
If a break is inserted, the part after it will be skipped in the last time playing.
	The break symbol highly varies, so check for the driver you're using.
<details><summary>Example:</summary>
 
```
l4 [adr8>d<b8 adr8ag8 f+8f+8g8a8de |f+1]2 d1
```
(audio)
</details>

### Channel Loop Position
Syntax: `L`
This will loop the selected channel from the current point onwards after it reaches its end.
You don't need to put in the same point for all channels. Just make sure you know what you're doing or the channels will desync after the loop ([unless that's your intention](https://youtu.be/EjLWJIFM1ho))
<details><summary>Example:</summary>
 
```
l8 defg a4>d4 fecd <a4>c<a L gfga d4cd f4g4d2
```
(audio)
</details>

### Volume
Syntax: `v(value)`
Changes the volume. The value reach will depend on the driver and soundchip you're using.
Depending on the driver there'll be other ways to change the volume as well, so look into that!
<details><summary>Example:</summary>
	
```
l8 v10 defg v8e4 v3 c v15 d^1
```
(audio)
</details>	

### Panning
Syntax: `p(value)`
Usually the values are 1 = Right, 2 = Left and 3 = Center. But when the soundchip doesn't have hardpanning it might be negative values for left and positive values for right!
<details><summary>Example:</summary>
	
```
l8 v10 defg v8e4 v3 c v15 d^1
```
(audio)
</details>	

### Precise Note Length
Syntax: `%(value)`
Lets you use a precise number of ticks for your note length.
Unless you change it, in most cases a quarter note = 24 ticks, so use that as a basis for calculation.
This helps making fast arpeggios and the like, but it can result in channel desyncing if you don't calculate it correctly.
<details><summary>Example:</summary>
	
```
l%1 [eb->e-garr bg+fc+<g+rr]8
```
(audio)
</details>	

### Gate Note setting
Syntax: `q(x)`
This makes the note rest x number of ticks before its end.
This is useful when you want to reset envelope in FM instruments, or want a release before the note ends.
<details><summary>Example:</summary>
	
```
l16 q0 cdefg q1 cdefg
```
(audio)
</details>	
	

Sometimes there's also a `Q(1-8)` option, which rests the note after x/8ths of its length.
This allows for a "stacatto-like" effect.
<details><summary>Example:</summary>
	
```
l16 crdrerfrg8r8>c8r8 l8 Q4 <cdefg4>c4
```
(audio)
</details>	

