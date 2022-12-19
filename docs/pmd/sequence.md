# Sequencing in PMD

This page is an addendum to the topics in the Basic MML Sequence part of the manual. If you haven't, read it first.

---

## The Basic Sequence

### Notes

Notes are `a b c d e f g`, and a rest is `r`. A repeat note is `x`, and will play the same note as the previous one typed.

**Sharps and Flats (`+` and `-`)** are treated relatively, allowing them to be stacked as willed. This means one can, for example, write `a++` which will result in the same as a `b`. This is equivalent to double sharps and flats, and beyond, in music.

There's also a **key signature command**, which will automatically sharpen or flatten the defined notes:
```
G   _{+fcg}         ;Auto sharps f, c, and g
G   l8 ab>cd efga4  ;Plays the A major scale
```
The key signature will be ignored if a `=` is attached to the note:
```
G   _{+fcg} ab>cd efg=a4  ;g natural plays instead of g+
```

Since PMD's sharps and flats are treated relatively, this also means the opposite symbol (`-`) could be used to make the `g` natural as well. However, in the key signature command it's absolute, so only a `=` can reset the notes.

```
G   _{=fcg}         ;Resets the auto sharp of f, c, and g
G   l8 ab>cd efga4  ;Plays the A minor scale
```

### Note Lengths

Independent of the tempo of the song, **PMD uses a fixed tick length for the whole note** (96 by default). Note lengths will divide that number, meaning the possible note lengths that will be exactly the desired length are:

```
1, 2, 3, 4, 6, 8, 12, 16, 24, 32, 48, 96
```

To se the default length value, use `l<length>`.

**Ties and slurs** are done with `&`. It supports having only the note length after it to extend the note's duration.

Dotted notes are also supported, and you can stack them as long as it's divisible, and it'll keep adding halves to the note's length. Starting with a half, then a quarter, an eighth, a sixteenth, etc.
```
G   g4...   ;equals g4&8&16&32
```

One can also notate note lengths using ticks for more precise timings. To do so, one a `%<value>` is attached to the note or `l` command.
```
G   g%45   ;equals g4...
```

But keep in mind the tick count to avoid desyncing problems.

---

### Octaves

Octaves are defined by the `o<value>` command.
The range is **1–8** except for ADPCM, which is **1–7**. 

The **default octave** is `o4`. The default for other commands can be changed by the octave setters.

Using + or - before the value will change the current and all the following octave commands by that value:
```
H o-1           ;Subtracts 1 from the following octaves
GH o4 cdefg     ;H will play at octave 3 instead of 4
```

By default, `>` increases and `<` decreases octaves. This can be reversed with the `#Octave Reverse` header, or using the `X` command to toggle the direction, if one wishes.

---

### Loops
PMD's loop format is:
```
[<sequence> : <sequence> ]<value>
```
Loops repeat the content in the brackets `<value>` times and if there's a break `:` it'll skip what's after on the last loop.

PMD can nest loops up to 255 levels deep, and how commands behave for each loop depends on each command:

- Commands that affect notes and lengths will reset to their previous during the loop.

```
l4 [cdef l8 gab>c]2 ;On loop cdef will still have length 4
```

- Effect commands will not reset.
```
[cdefg MP-20]2   ;The MP command affects the notes after the loop
```

If the loop count value is ommited, **it'll loop infinitely by default**. One can change this behavior with the `#LoopDefault <value>` header, which will set a default loop count value.

---

## Common Commands

### Tempo (`t<value>`)

**`t<value>`** **sets/changes the tempo** by an absolute value, or by adding or decreasing by adding a + or - to the current value.

Same as the `#Tempo` header, it uses a half-note as the beat instead of a quarter note, meaning the desired BPM has to be halved to get the value to enter.

```
G   l8 t50 cdefg t+5 cdefg   ;sets the BPM to 100, then 110
```
---

### Volume (`V<value>`, `v<value>`, `(`, `)`)

**`V<value>`** is the default command for **setting volume**. Its range depends on which channel you're sequencing.

 - 0–127 for FM channels
 - 0–255 for ADPCM and 86PCM
 - 0–15 SSG, SSGPCM and PPZ

**`v<value>`** works as a **coarse setting**, ranging from 0–16 (15 for SSG and SSGPCM). The values correspond to the audible range of the type of channel it's for, making it more universal and simpler to type, at the cost of not being as precise.

```
A   v12 gdefg   ;v12 corresponds to V117 on the FM channels
```
The manual has a [table specifying what each value corresponds to](https://pigu-a.github.io/pmddocs/pmdmml.htm#5-1).

Same as octaves, adding a + or a - before the value in the lowercase `v` command will change the current and all the following volume commands commands by that value. **This is a fine value, like a `V` command, not `v`.**

**`)`** and **`(`** respectively increases and decreases the volume in the same increments as `v`. You can do it in `V` increments by adding a `%<value>` after.

```
A   v12 cdefg ) cdefg ;Is the same as v117 cdefg V121 cdefg
A   (%13 cdefg        ;               V108 cdefg
```

One can also add a `^` or `^<value>` after it to only change the volume for the next note, returning to normal for the one after. One utility for this is 1-channel echo effects:

```
G     l16 t90 v13 c r d (^3c e (^3d f (^3e g (^3 f r (^3g
```

---

### Transposition/Modulation (`_<value>`, `__<value>`, `_M<value>`)

In PMD, there are 3 types of transposition:

**`_<value>`** transposes the following written sequence by the given amount of semitones:
```
G  _+2 cdefg   ;Transposes to def+ga
```
**`__<value>`** is a **relative transposition**, meaning it'll add or subtract from the current transposition setting, allowing it to continually transpose even on loops. For example:

```
;The _<value> command acts on the sequence as is, setting the transposition value, so
G  [ga:b _+5]3         ;This will sound like "gab >cde cd"

;The __<value> command acts relatively on the current transposition value, so
G  [ga:b __+5]3        ;This will sound like "gab >cde fg"
```

**`_M<value>`** is a **channel transposition command**, so it transposes everything after it, including the transpose commands. As such, channel transposition and regular transposition add together.
```
G  _M+2 cde _+5 cde __+5 c+d   ;Will become def+ gab c+d
```

---

### Quantization (`Q<value>`, `q<value>`)

PMD has both coarse and fine quantization methods.

**`Q<value>`** plays the following notes for only `<value>`/8 of its length and cuts the rest of it.
```
G  l8 Q8 cdefg ;The note plays normally
G  l8 Q4 cdefg ;It sounds as if l16 crdrerfrgr was typed instead
```

**`q<value>`** subtracts the specified number of ticks from the end of the note length and cuts it.
```
G  l8 q0 cdefg ;The note plays as normal
G  l8 q1 cdefg ;The note cuts just before the next
```

There's a lot more to **`q<value>`**, but as it's complicated and situational, it'll be saved for a future version of the guide.

---

### Detune (`D<value>`, `DD<value>`, `I<value>`)

There are 3 types of Detune in PMD:

**`D<value>`** is the main detune, which will change the tuning of the following written sequence by the given amount of units, according to the chip's tuning frequency table. This means it's inequal across types of channels and note you're in, for different types use different units.

```
G   o2 D0 c D-2 c   o4 D0 c D-2 c    o6 D0 c D-2 c
;  The same detune becomes makes a more drastic change on higher notes
```

**`DD<value>`** is a relative detune, meaning it'll add or subtract from the current detune setting.

```
G   o4 D0 c D-2 [ c  DD-2]4
```

**`I<value>`** (uppercase i) is another detune command, which tries to mantain the same level of detune accross the complete range. `I` **will not function** unless the **`#Bendrange <value>`** header or **`B<value>`** command is used. Allowing a total range of -32768–32767, the values 0–8192 represent a detuning range over the number of semitones specified by `#Bendrange` or `B`.
```
G l8 B1 I0 c I4096 c I-4096 c   ;Tries to play c, then c half-sharp, then c half-flat
```
**This detune method should be used sparingly**, for it is prone to being inaccurate and producing incorrect tones, especially on SSG channels.

---

### Channel Loop (`L`)

Loops everything from this command up to the end of the channel forever.
```
G  o4 L cdefg      ; cdefg loops endlessly
```
**The same rules as the nested loop apply**. Commands that affect notes and note lengths get reset and effect commands does not. It's good to reset the commands to their default or previous value after the channel loop command.

If using `/C` in the `#Option` header, along the whole song's length, it'll also specifically display the length of the loop, so it's good to pay attention to whether the lengths match for a perfect loop.

---
## Additional Notation Commands

### Portamento (`{<start note><end note>}`)
Portamento, or pitch slides/bends, work by enclosing 2 notes in `{ }` brackets.

```
G  l8 o4 cdef {g>c}4. c
```

They behave as one note, so they'll use the default length if you only use the brackets, or the note length you attach to it. You can also tie into and out of them:

```
G  l8 o5 c<ba16.&{ag}32g fed16.&{dc}32c
```

Alternatively, you can also add the delay to the portamento itself by adding a note legnth value after a comma. This will make the portamento hold the first note for that specified length, then do the portamento over the remaining length:
```
G  l8 o5 {gc}4,8..c4defge ;Expands to g8..&{gc}32
```
When doing this, the first value represents the whole length of both the first note **and the portamento**, so the second length shouldn't be longer than the second.

---

### Retrigger/Echo (`W<value>,<value>`)

This retriggers the following notes every number of ticks specified in the first value, decreasing or increasing the volume by the second value. If the second value isn't specified, it defaults to -1.
```
G  v15 W6 d1         ;Is the same as v15 [d16 (]16
G  v1  W6,+1 d1      ;Is the same as v0  [d16 )]16
```
A third value can be added, which will change the behavior a bit, as follows:
```
0 = Retrigger notes  ;default
1 = Tie notes        ;notes will be tied while changing volume
2 = Retrigger once   ;only one additional note will be played
3 = Tie notes once   ;same as above but with notes tied
```

Using `W0` will disable the command.

---
### Grace Note (`S<ticks>,<semitones>,<tie>`)

The grace note effect will automatically add a glide over the number of semitones to the beginning of the notes following the command.

First, you specify the length of each semitone added, then you specify how many semitones to glide from (default -1):
```
G  v15 q1 S3,-2 d4      ;it's the same as c%6&c+%6&d8
```
An additional third value can be added to disable tying (which is the default) by setting it to 0.

```
G  v15 q1 S3,-2,0 d4    ;it's the same as c%6c+%6d8
```

Using `S0` will disable the command.

---

### Arpeggios (`{{<notes>}}<length>`)

The arpeggio command does a fast (%1 speed) arpeggio between the specified notes that lasts for the specified length:
```
G  @5 v13 {{ceg}}2        ; plays a ceg arpeggio for the length of a half note
```
The specified length can't be above 255 ticks.

It can also be expanded to a total of 5 values:

After the length of the arpeggio, you can add the length for each tick (`%` use is recommended)

```
G  @5 v13 {{ceg}}2,%3     ; notes play slower than the default %1ç
```

After that you can specify if the arpeggio will be tied (`1`,default) or not (`0`)

```
G  @6 v13 {{ceg}}2,%6,0  ; notes are triggered individually
```

Then you can add a rest at the end for the specified number of ticks

```
G  @0 v10 [ {{ceg}}8,%3,1,1 ]4 ; a quick rest before each arpeggio
```

And finally, you can specify how much the volume will decrease (-) or increase (+) each time the arpeggio plays
```
G  @0 v13 {{ceg}}2,%3,1,0,-1 ; volume decreases gradually
```

---

### Fast-forward (`"<sequence>"`)

This will skip anything written between the quotation marks. This is useful if you're using the DOS or PMDDotNET setup and want the song to start at a given point.

This can be used at any point of the MML except within headers and instrument definitions.

```
"
A  defga     ;Skips everything between the quotation marks
"
A  cdefg     ;Then starts here
```
One could use the multi-line comment format with `` ` `` for achieving a similar result, but the important difference is that this will keep the command progression of the song

```
`                     ;Comment begins
G	cdefgab>c< MP-80  ;Completely ignores everything
`                     ;Comment ends
G	def+gab>c+d<      ;Sequence plays without the MP effect

"                     ;Fast-forward begins
G	cdefgab>c< MP-80  ;Skips this sequence
"                     ;Fast-forward ends
G	def+gab>c+d<      ;Sequence plays with the MP effect.
```
