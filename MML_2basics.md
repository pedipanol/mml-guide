## Part 3: This Guide

This part of the guide is focused on general concepts that you'll have to familiarize yourself with to write music in MML. The explanations here will be more broad but it's important as a basis once you start learning a specific driver, and so you know what to look foor.

## Part 4: The Structure

There are 4 distinct elements in a MML structure  not necesseraily be in the following order:

### Header:
This will contain **metadata** about the song as well as compilation instructions. In here you may define a song's title, insert a comment that will be displayed on the compiled file, set things such as the main tempo, etc.

### Instrument Definition/Instrument Macros:
This will create the instruments and tables that to be called with their respective commands. Often times there'll only be one type of definition, but particularly in PSG soundchip it's common to have different macro types for different aspects. 

### Sequence Macros:
These are sequences you can call with their respective commands. It's useful to save time and typing and there are many ways of doing it. These

### Channels and Sequence
Assigning channels and which sequences they're tied to

## Part 5: Instrument Definition

As said above, this will vary a lot depending on the driver being used, but the general concept is that when you want a channel to use a certain instrument you'll call it by its number and/or name. The command used will be the same character you used to set it, most often a `@`

Some drivers will have use instrument macros, requiring you to use 2 or more separate commands to call them.

Some drivers or channels won't need this setting for a simple sequence, as they support a blank instrument or might have some prebuilt ones. But more often than not if you use a value not assigned to a setting it'll result in a compilation error or will mute the current channel so it's important to keep it in mind.

## Part 6: The Basic Sequence

As said in Part 1, modern MML's basic syntax derives from sheet music. It's not necessarily made to be read by peformers such [ABC Notation](https://en.wikipedia.org/wiki/ABC_notation) or for full creation of sheet music like [Lilypond](https://en.wikipedia.org/wiki/LilyPond), but many of its core elements are made having sheet music notation as its conception. So the best way to explain the basic syntax is with sheet music.

_I'll see if I can make an explanation easier for tracker musicians who don't know how to read sheet music in the future._

Most of what's contained here is **basically universal** among all syntaxes. Some of them might tackle things diferently, some of them might **not support** one or two things. If you want to try things out as they're explained, **please follow the Setting Up section in PMD's page** which is the format I'll be using for this explanation (write the sequences in channel G so it doesn't require additional setup). Audio examples will also be provided.

For this guide we'll be using a familiar song as a basis.

![fleentstones](/images/granddad.png)

### Notes and Note Lengths

As usual, the notes are:
```
a b c d e f g
```
And something to reinforce early is that **MML is case-sensitive** in most drivers. All notes must be typed in lowercase as to, for example, not be treated as a command that may use the capital letter.

**Note lengths** are specified by a number **after the note**. Similar to sheet music, they operate by dividing a whole note by that number. So
```
1 = Whole note | 2 = Half Note | 4 = Quarter Note | 8 = Eigth Note | and so on...
```
The range of values you can input will vary depending on the driver and the **tick count** for the whole note. Most drivers will have **96 ticks per whole note** as default, as they can be divided by a wide range of numbers, but you can change it as you please.

From these two we can start transcribing the song above:
```
a4d4
```
Now we need a **rest**. For that we'll use the letter `r`. It works **the same as a note**, so you'll assign a length to it too, i.e. `r8`.

Let's continue:
```
a4d4r8d4b8 a4d4r8a4g8
```
>Notice that in the first measure I chose to type the high `d` as `d4` instead of tying 2 `d8`'s. This is just because it would be redundant. One could use the tie `&` and write it as `d8&d8` if desired.

For the next part we'll need a sharp `f`. For sharp and flat notes, we simply add a `+` or `-` after the note respectively. The note + sharp is trated as a note input by itself, so it's **always before the length**.
```
a4d4r8d4b8 a4d4r8a4g8 f+8f+8g8a8d4e4 f+1
```
>Notice that I used a space when the measure ended. **You freely space the script** as the result will be the same, for the most. Each format will differ in the specifics they don't approve, but as a rule of thumb:
>1. Don't put space between numbers
>2. Don't put space before sharps/flats.
>3. Don't put space before dots.
>3. Don't put space before command values.
>
> Spacing can greatly improve readability of your script. Personally I like using spaces to limit a measure or a beat in really fast sections. I also use it often when there are 2 parts playing the same rhythmic patterns. There's a huge variety of ways you can do it.


Before we wrap up this section let's simplify the MML with one command: **default note length** (`l(value)`). This will set a default length value so you don't have to keep typing the same value. It's good to anticipate which note will be the **most frequent** and set it as the default to save yourself time. In this case both 4 and 8 appear 8 times so it's down to preference. I'll go with `l4`.
```
l4 adr8db8 adr8ag8 f+8f+8g8a8de f+1
```

#### Octaves
If you tried playing what we wrote you've probably noticed that a note is playing in the **wrong octave**. Let's fix that.

To **set** the current octave we use the `o(value)` command. This will change the current octave to a **fixed** one and everything that comes after will use it as a basis (**absolute change**). Most drivers limit the range to 1-8  for simplicity's sake.
```
o4 l4 adr8db8 adr8ag8 f+8f+8g8a8de f+1
```
Usually `o4` is set for the middle c, but it can vary.

We can also change the octave relative to the previous one with the `<` and `>` signs, which makes it easier for short changes (**relative change**). The default behavior is `<` decreases it and `>` increases. There are some exceptions but usually you can change this with a header.
```
o4 l4 adr8>d<b8 adr8ag8 f+8f+8g8a8de f+1
```

#### Loops
_ignore this part if you want to use AddMusicK._

Loops work similar to how repetition works in sheet music, by setting a beginning and end and a break that for quitting it at the last time it's played.

For all MML formats you set the start and end of a loop with `[ ]`. The basic syntax looks like this:
```
[ <sequence> <break> <sequence>]<number of repetitions>
```
The break symbol varies depending on the format. In PMD's case it's `:`.

Let's apply it to the song
```
l4 [adr8db8 adr8ag8 f+8f+8g8a8de : f+1]2 d1
```

This won't be needed for this song, but in most formats you can nest as many loops as you want. This can help save a lot of typing and space. If we wanted to play the whole thing twice we could type:
```
l4 [[adr8db8 adr8ag8 f+8f+8g8a8de : f+1]2 d1 ]2
```

>How exactly the loops behave in regard to resetting commands and etc. depends on the driver, so I'll be sure to point out on each's page. For example, PMD doesn't reset them when looping so if you type:
>```
>[cdefgb MP-80]2
>```
>The effect will be active the second time it plays.

Just to get finished with the basic sequences, there are 2 things that we won't be needed for this song specifically, but are still part of the basic sequence:

### Tie/Slur
Ties or Slurs are used both to change notes without retriggering the instrument/envelope and to extend a current note length. In PMD they're done with a `&` but it's not uncommon for drivers to use `^`  too.

Example 1:
>```
>c8 & d8 e4
>```
> Result: `d` will play without retriggering t he instrument, while `e` will.
 
 Example 2:

>```
>c8 & c8
>```
> Result: it's the same as a `c4`

When the goal is just to extend the note length, some drivers allow you to just add a length value instead of the note after the tie. Others might have a different command specific for that (usually for saving space in compilation), but the ties will always allow for that.

### Dotted Notes
By adding a dot `.` after the note or note length, it'll multiply its length by 1.5x, as it does in sheet music. Some drivers even allow you to stack them up, but it's not very common.

Example:
>```
>l4 ad.>d<b8 ad.ag8
>```
>Result: the same as placing a tie with an `d8` after

## Part 7: Common Commands

This is an explanation on the most frequent sequence-related commands you'll find across all formats. All the examples will be done with PMD, but they'll apply to many others, at least in concept.

Title format is `Command Name (Common Representation if applicable)`. 

These commands will affect everything that comes after it, requiring a reset to the previous value if one wishes to return to how it was previously.

### Tempo ( T[value]  t[value] )
This sets the tempo of the song from that point forward. Most often the BPM itself but it can be the internal timer.

Using + or - before the value turns it into a relative change.

Example:
>```
>l8 t150 cdefg t+20 cdefg
>```
>Result: will sound the same as:
>```
>l8 t150 cdefg t170 cdefg
>```

### Volume ( V[value] v[value] )
This sets the volume of the current channel from that point forward. The value range will depend on the driver and/or soundchip.

Like tempo, more often than not you can  change it relatively with + or -. Many drivers also use `(` and `)` for changing it relatively.

Example:
>```
>l8 v13 cdefg v10 cdefg v+3 cdefg ((( cdefg
>```

### Transposition
There are 2 main types of transposition commands: Normal Transposition and Channel Transposition.

Normal transposition will make it so everything placed after it will be transposed by + or - x semitones.

Example:
>```
>l8 cdefg _+2 cdefg
>```
>Result: it sounds the same as
>```
>l8 cdefg def+ga
>```

Channel transposition behaves the same, but it'll include the relative transpositions without any conflict. Because of that it's good to use sparingly and use the normal transposition until you want to transpose a song or a whole section that already uses transposition commands on it.

Example:
>```
>l8 cdefg _M+2 cdefg _+2 cdefg
>```
>Result: it sounds the same as
>```
>l8 cdefg def+ga ef+g+ab
>```

These commands are big time and typing savers. Useful for playing the same line in other keys or writing something in a key with less sharps or flats, as to have to type + or - less often.

### Detune ( D±[value])
This will detune the current channel by + or - steps from that point forward.

Most often this will change the value according to the soundchip's pitch range instead of a linear one, so how much true detune it amounts to will change depending on which point at the range you are. Usually the higher the note is, the stronger the detune will be.

>```
>l2   o2 D0 g D-2 g   o6 D0 g D-2 g
>```
>Result: the second one is at higher range so the detune change is much more noticeable.

### Quantization ( Q[value] q[value] )
Quantization in MML is used to automatically cut notes within their original lengths. There are 2 possible ways of doing it (usually both are supported): corase and fine.

With the coarse setting, (in PMD, an upper case `Q`) it takes the note and only plays for x/8 of its length. This works like a stacatto in musical notation, except you can specify how much of the note you want played.

Example:
>```
>l8 Q8 cdefg Q4 cdefg
>```
>Result: will only play half of its length. The second part would be the same as if a `l16 crdrerfrgr` was typed instead.

With the fine setting, (in PMD, a lower case `q`) it'll subtract x ticks from the ending of the note.

Example:
>```
>l8 q0 cdefg q1 cdefg
>```
>Result: the quantization makes the instrument release 1 tick before the next note, allowing the envelope to reset without a noticeable rest.

As the example shows, the fine quantization is particularly useful in FM drivers, as it'll allow the envelope to reset in situations it wouldn't. It also can help emphasize any instrument's attack with the momentary silence right before the note, notably used in Megaman's NES soundtracks.

When there are ties/slurs, this command will account for the whole tied length, so:
```
l4 Q6 c&de
```
Will sound the same as
```
l4 Q8 c&d8r8e
```

## Part 8: Sequence Macros

Sequence macros are useful for saving time and typing, making the driver play the set sequence whenever it's called. There are many situations where they can be useful:

Setting many commands with a single one. This is really handy for making drums, which usually will take many commands at once.

Example:
>```
>!b	@1 MP-80 v10 o1 _+9 Q4 \vh12 \b\h 
>!s	@2 MP-40 v13 o4 _+7 Q8 \vh12\vs15\s\h
>!H	@4 *0 	 v12 o7 _0 Q8 \c
>!h	@5 *0 	 v10 o7 _0	Q4 \vh6\h
>
>A	l8 !b c !h c !s c !h c !b c c !s c !H c
>```
>The above example is a typical FM drum setting in PMD. Each macro (represented by `![character]`) contains respectively:
> 1. Change instrument
> 2. Pitch Down/turn off command
> 3. Volume
> 4. Set octave
> 5. Transpose
> 6. Quantization
> 7. Play the corresponding rhythm channel
>
>This allows this huge sequence of commands that would have to be set in every drum part to be summarized in a single command, which will be applied to the note that comes after the macro in the sequence, making this kind of sequencing become much more simple.

They're also useful for playing repeated patterns which can be transposed with a command.

Example:
>```
>!a rgb>d [f+gf+d]3<
>
>A l8 o3 !a !a _-7 !a !a
>```
>Result: the macro plays twice and then it plays twice again transposed down.

Among many other uses.

Most times the sequence in those macros are added to the main sequence during compilation. So they'll operate like any normal sequence. Remember to keep track of what commands you're using in one in case it results in any unintended change.

Very few drivers will have Absolute Macros, which are returned to instead of added in the sequence. This helps save space but also limits the freedom you have with macros a bit, mainly when it comes to relative changes. Definitely use them for drums if your driver supports it, since the example setup increases the file size drastically. 