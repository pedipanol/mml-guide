# Tips and Tricks

Here's a list of some things that aren't actually explained in the manual but is good knowledge to have. Send me a message if there's anything you'd want me to cover in here.

This is just an overview of how I approach things and not an objective how-to thing. If you've read the guide you can find your own way of doing things!

## Ensuring Channel Syncrony

I already taked about how the having `/c` on the `#Options` header is important. This is because it'll show the total tick count of each channel, and facilitate spotting mistakes in syncrony right at the time of compilation.

This is easier when you work in a way that priorizes syncrony, here are some tips for that.

### Finish writing the current bar before compiling

The bar length having a round number of ticks, be it the whole note length or another, makes a good reference point. When you write a whole bar, if there's a mistake it won't result a multiple of the intended length, so the mistake is more obvious

Example: at a bar length of 96 ticks and writing 2 bars.

```
Part A  Length : 189
```

Part A obviously has a mistake in there.

### Write the song in sections

Some people (mainly who are transcribing into the MML) prefer to write completely each channel's sequence at a time. This is a fine workflow that lets you focus in individual channels. But when you write a whole section at a time, you'll have other channel's lengths as a reference for finding mistakes.
```
Part A  Length : 768
Part B  Length : 768
Part C  Length : 756
Part D  Length : 768
```
Part C has a mistake.

If you also group the channels in sections you'll have the benefit of being able to skip the song to to specific sections.

```
"    ;Everything between the quotation marks will be skipped
G   @6 o5 l16      cdefg4
H   @5 o3 l8       c c g4
I   @6 o4 l8 MP-80 c c c c
"
G   gab>cd4
H   g g >d4
I   c c  c c
```

Which can also be used to find mistakes.

### Organize and space your script

I joke about MML being an acronym for "Many Messy Letters", but you don't want it to be a mess for you, so find a way to organize your script so it's easy to read. This is vital for making mistake troubleshooting faster.

There's no one batter way to do it, I don't even do it in one consistent way, instead going according to the needs of each part I'm writing. But for the sake of example, let's look at the above sequence again:

```
G   @6 o5 l16      cdefg4
H   @5 o3 l8       c c g4
I   @6 o4 l8 MP-80 c c c c

G   gab>cd4
H   g g >d4
I   c c  c c
```

In it, I not only separated the script in sections but also used spacing so all notes that hit at the same time are in the vertical space.

If there was a typo, say:
```
G   @6 o5 l16      cdefg3 ;<- 3 instead of 4
H   @5 o3 l8       c c g4
I   @6 o4 l8 MP-80 c c c c

G   gab>cd4
H   g g >d4
I   c c  c c
```
As soon as I saw the wrong section length, or heard the desync in the song, I'd identify quickly.

Anyways the point is, making the script easier for you to read will make it easier to fix, so make sure to do it!

## Making FM Drums

To make FM drums we create macros containing a set of commands to be activated on the following note:

```
!b	@1 MP-80 v10 o1 _+9 Q4 \b         ;Kick
!s	@2 MP-40 v13 o4 _+7 Q8 \s         ;Snare
!H	@4 *0    v12 o7 _0  Q8 \c         ;Hihat Open
!h	@5 *0    v10 o7 _0  Q4 \h         ;Hihat closed

A	l8 !b c !h c !s c !h c !b c c !s c !H c
```

Let's break down the commands in each:

 1. Change instrument (`@`)
    
    Those are specific drum FM instruments, that will sound different/dry without the extra configuration.

 2. Set pitch fall (`MP`), or turn it off (`*0`)
    
    `MP` makes the quick pitch down adding the punch to the snare and kick, and `*0` turns it off for the hihats.

 3. Volume (`v`)
    
    Changes the channel volume to the desired for each drum.

 4. Set octave (`o`)

    Changes the octave to the appropriate one for each drum. Since they will sound completely different in other than the intended.

 5. Transpose (`_`)

    Since I'm not including the note in the macro, putting it after instead, the transpose command makes it so I can only put a `c` after every macro instead.

 6. Quantization (`Q`)

    To cut drums faster

 7. Rhythm Channel Trigger (`\`)

    To play the rhythm channels at the same time as the FM ones, adding an extra punch to them.

All of these commands and values are not universal, making it so you have to change them depending on the drums instruments you're using or what kind of song you're sequencing.

There's no limit of macros so you can also make nuanced ajustments for more variety.

It's important to note that this can increase the filesize dramatically, so in the rare instance you want to keep a really low filesize, you might want to reduce the amount of commands in each macro.

## Making SSG Drums

If you're not content with PMD's internal SSG drums, you can make your own, with some similar concepts to the FM drums, but a bit more difficult.

Here I'll explain a set I've made:

```
!bd v15 o2 E31,31,17,15,2 P2 w24 c%1 P1 MP-72 c%11 ;Kick l8
!bs v15 o2 E31,31,17,15,2 P2 w24 c%1 P1 MP-72 c%5  ;Kick l16
!s  v15 o4 @5 P3 w12 MP-48 d8                      ;Snare l8
!hd v14 P2 w0 @6 c%1 r%11                          ;Hihat l8
!hs v14 P2 w0 @6 c%1 r%5                           ;Hihat l16
!o  v15 P2 w0 @5 c%9 r%3                           ;Hihat open l8

I t100 [!bd !hs !hs !s !hd !bd !bd !s !hd !bd !hd !s !hd !bd !bd !s !o]8
```

So... this is more varied than the FM ones, let's break down each one, starting with the Kick:

```
!bd v15 o2 E31,31,17,15,2 P2 w24 c%1 P1 MP-72 c%11 ;Kick l8
!bs v15 o2 E31,31,17,15,2 P2 w24 c%1 P1 MP-72 c%5  ;Kick l16
```
Here the sequence is:

1. Sets the volume (`v`) and octave (`o`)
2. Sets the ADSR volume envelope (`E`)
3. Puts channel in noise mode (`P`) and sets the frequency (`w`)
4. Plays the noise for 1 frame (`c%1`)
5. Changes the channel to square wave mode (`P`)
6. Adds a pitch-down effect (`MP`)
7. Plays the square for the rest of the note (`c%11`,`c%5`)

Since I needed the 1-tick noise for the additional effect, I had to include the notes into the macro itself, so I made a copy changing the last note's tick length.

The snare has the same principles but it's simpler:

```
!s  v15 o4 @5 P3 w12 MP-48 d8
```

1. Sets the volume (`v`) and octave (`o`)
2. Sets the volume envelope using a built-in preset (`@`)
3. Puts the channel in Square+Noise mode (`P`) and sets the frequency (`w`).
4. Adds a pitch down effect (`MP`)
5. Plays the snare (`d8`)

As it's a command list affecting a single note, I could have left it outside the macro, but for consistency with the kick, I prefer leaving it inside.

Both the hihats operate in the same way:
```
!hd v14 @6 P2 w0 c%1 r%11
!hs v14 @6 P2 w0 c%1 r%5 
!o  v15 @5 P2 w0 c%9 r%3 
```
1. Sets the volume (`v`) 
2. Sets the volume envelope using a built-in preset (`@`)
3. Puts channel in noise mode (`P`) and sets the frequency (`w`)
4. Plays the note for 1 frame (`c%`) (9 frames on the open hihat)
5. Cuts for the rest of the note (`r%`)

Unlike FM where you'll only see variety in the values used in those macros, there's a huge variety in how to make SSG drums. For example I could add the rhythm trigger commands in there for them to play in sync with teh SSG. So try things out for yourself to get interesting results!


## Making PCM Drum Macros

Usually PCM drums should sound as intended after finding the note that plays the right frequency. However doesn't always happen, and sometimes you might want to tweak the drums a little a little anyway, so it's worth addressing it here.

```
!b	V30 @0 _-4  \h
!s	V50 @1 _-3  \h
!h	V30 @2 _0   \h
!B	V50 @3 _-12 \h
!S	V50 @4 _-10 \h    

J   l8 o7 [[!B c4 !s c !b c !h c !b c :!s c !h c]2 !S c4 ]2
```

1. Sets the volume for each drum (`V`)
2. Changes the sample (`@`)
3. Transpose accordingly (`_`)
4. Play a hihat on the RSS (`\h`)

It really is that much simpler since the samples are already doing the biggest part of the work.