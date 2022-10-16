# Sequencing in PMD

This page is an adendum to the topics in the Basic MML Sequence part of the manual. If you haven't, read it first.

## The Basic Sequence

### Notes

`a b c d e f g` are the notes and `r` are rests. `x` is a repeat note, and will play the same note as the previous one typed.

**Sharps and Flats (+ and -)** can be stacked at will. Meaning one can, for example, write `a++` which will result in the same as a `b`.

There's also a **key signature command**, which will automatically sharpen or flat the defined notes:
```
G   _{+fcg}         ;Auto sharps f c and g,
G   l8 ab>cd efga4  ;Plays the A major scale
```
The key signature will be ignored if the opposite symbol or `=` is attached to the note.
```
G   _{+fcg} ab>cd efg=a4  ;g natural plays instead of g+
```
### Note Lengths

Independent on the tempo of the song, PMD uses a fixed tick length for the whole note (96 by default). Note lengths will divide that number, meaning the possible note lengths are:

```
1, 2, 3, 4, 6, 8, 12, 16, 24, 32, 48, 96
```

`l<length>` sets the default length value.

**Ties and slurs** are done with `&`. It supports having only the note length after to extend the note's duration.

You can stack dots as long as it's divisible, and it'll keep multiplying the current note length by 1.5x.
```
G   g4...   ;equals g4&8&16&32
```

One can also do notate note lengths using ticks for more precise timings. To do so, one a `%<value>` is attached to the note or `l` command.

### Octaves

Octaves are defined by the `o<value>` command.
The range is 1-8 except for ADPCM, which is 1-6. 

The default octave if the command is not present is `o4`.

Using + or - before the value will change the current and all the following octave commands by that value:
```
H o-1           ;Subtracts 1 from the following octaves
GH o4 cdefg     ;H will play at octave 3 instead of 4
```

By default, `>` increases and `<` decreases octaves. This can be reversed with the `#Octave Reverse` header, or using the `X` command, if one wishes.

### Loops
PMD's Loop Format:
```
[<sequence> : <sequence> ]<value>
```
Loops repeat the content in the brackets \<value> times and skips what's after the break `:` in the last loop if wanted.

PMD can nest loops infinitely and how commands behave in looping depends on each command:

 - Commands that affect notes and lengths will reset to their previous during the loop.
    ```
    l4 [cdef l8 gab>c]2 ;On loop cdef will still have length 4
    ```
 - Effect commands will not reset.
    ```
    [cdefg MP-80]2   ;The MP command affects the notes on loop
    ```

If the loop count value is ommited, it'll loop infinitely by default. One can change this behavior with the `#LoopDefault <value>` header, which will set a default loop count value.

## Common Commands

### Tempo (t\<value>)

**t\<value>** sets/changes the tempo, by an absolute value or by adding or decreasing with + or - to the current value.

Same as the `#Tempo` header, it uses a half-note as the beat instead of a quarter note, meaning the value has to be halved to get the actual BPM.

```
G   l8 t50 cdefg t+5 cdefg   ;sets the BPM to 100, then 110
```
### Volume (V\<value>, v\<value>, ( , ) )

**V\<value>** is the default command for setting volume. Its range depends on which channel you're sequencing.
 - 0-127 for FM channels
 - 0-255 for ADPCM and 86PCM
 - 0-15 SSG, SSGPCM and PPZ

**v\<value>** works as a coarse setting, ranging from 0-16. The values increment in bigger values, making the change more noticeable and simpler to type.
```
A   v12 gdefg   ;v12 corresponds to V117 on the FM channels
```
The manual has a [table specifying what each value corresponds to](https://pigu-a.github.io/pmddocs/pmdmml.htm#5-1).

**) and (** respectively increases and decreases the volume in the same increments as `v`. You can do it in `V` increments by adding a `^<value>` after.

```
A   v12 cdefg ) cdefg ;Is the same as v117 cdefg V121 cdefg
A   (^13 cdefg        ;               V108 cdefg
```
Same as octaves, adding a + or a - before the value in the  lowercase `v` command will change the current and all the following volume commands commands by that value (fine value).

### Transposition/Modulation ( _\<value> , __\<value> , _M\<value>)

In PMD there are 3 types of transposition:

**_\<value>** transposes the following written sequence by the given amount of semitones:
```
G  _+2 cdefg   ;Transposes to def+ga
```
**__\<value>** is a relative transposition, meaning it'll add or subtract from the current transposition setting, allowing it to continually transpose even on loops, for example:
```
;The _<value> command acts only on what's written, so
G  [ga:b _+5]3         ;This will sound like "gab >cde cd"

;The __<value> command acts on the currently playing note, so
G  [ga:b __+5]3        ;This will sound like "gab >cde fg
```
**_M\<value>** is a channel transposition command, so it transposes everything after it, including the transpose commands.
```
G  _M+2 cde _+5 cde __+5 c+d   ;Will become def+ gab c+d
```

### Quantization ( Q\<value>, q\<value>)

PMD has both the coarse and fine quantization methods.

**Q\<value>** plays the following notes for only <value>/8ths of its length and cuts the rest of it.
```
G  l8 Q8 cdefg ;The note plays normally
G  l8 Q4 cdefg ;It sounds as if l16 crdrerfrgr was typed instead
```

**q\<value>** subtracts the specified number of ticks from the end of the note length and cuts it.
```
G  l8 q0 cdefg ;The note plays as normal
G  l8 q1 cdefg ;The note cuts just before the next
```

There's a lot more into **q\<value>**, but as it's complicated and situational, find it more at the Advanced Section.

### Detune ( D\<value> , DD\<value> , I\<value> )

There are 3 types of Detune in PMD

**D\<value>** is the main detune, which will change the tuning of the following written sequence by the given amount of units, accordingly to the chip's tuning table, which means it's inequal across types of channels and note you're in.
```
G   o2 D0 c D-2 c   o4 D0 c D-2 c    o6 D0 c D-2 c
;  The same detune becomes makes a more drastic change on higher notes
```
**DD\<value>** is a relative detune, meaning it'll add or subtract from the current detune setting.
```
G   o4 D0 c D-2 [ c  DD-2]4
```

**I\<value>** (upper case i) is another detune command, which tries to mantain the same level of detune accross the complete range.

## Additional Notation Commands

### Portamento ( {\<start note>\<end note>} )
Portamento, or pitch slides, work by enclosing 2 notes in `{ }` brackets.
```
G  l8 o4 cdef {g>c}4. c
```
They behave as a note itself so they'll use the default length if you only use the brackets, or the note length you attach to it. You can also tie into and out of them:
```
G  l8 o5 c<ba16.&{ag}32g fed16.&{dc}32c
```

Alternatively you also add the delay to the portamento itself is by adding a note legnth value after a comma. This will make the portamento hold the first note for that specified length:
```
G  l8 o5 c<ba16.&{ag}32g
```