# The Rhythm Channels

This page explains the 2 Rhythm methods PMD supports: the OPNA's Rhythm Sound Source (RSS) channels and PMD's Rhythm Channel `K`.

## Rhythm Sound Source Commands

In PMD, the RSS can be controlled from any PMD channel by using the trigger command `\<perc>`:

`<perc>` corresponds to which rhythm channel will be used, as follows:

- `b` - Bass Drum
- `s` - Snare
- `t` - Tom
- `h` - Hi-hat
- `c` - Cymbal
- `i` - Rim Shot 

Those commands are set to be triggered upon the next note or rest:

```
I   t75 l4 \b r \s r \b r8 \b r8 \s r
```

This way, you can trigger multiple drums at the same time in a single channel by simply stacking the commands:

```
I   t75 l8 \b\h r \h r \s\h r \h r \b\h r \b\h r8 \h\s r \c r
```

To halt an ongoing sound in the next note, simply add a `p` after it. If this is not used, the percussion will continue to play after its corresponding note/rest ends.

```
I   t50 [\b\h r12 \h r24 \c r8 \h\b\s r12 \h r24 \c r12 \cp\h r24]4
;                        ^^                      ^^     ^^
;       The first cymbal doesn't get halted, the second does
```

Before moving on, keep in mind that "triggered upon the next note" also refers to loops; if you put a rhythm command at the end of a loop, it'll play on the first note of the loop and the next after the loop.

```
I   t50 [\b r \s r \h]2 \t r
;           ^      ^^      ^
; Hi-hat plays both at the first and next note
```

### RSS Channel Volume (`\v<perc><value>`)

This changes the volume of the specified channel individually. The value range is 0–31.

```
I   t75 l8 \vb20 \vs20 [\vh20 \b\h r \vh5\h r \vh20\s\h r \vh5\h r]4
;          ^^    ^^     ^^           ^^       ^^          ^^
;       Sets the volume for bass and snare drum, lowers the hi-hat's volume on off-beats
```

You can also change it relatively by using +/- before the value.
```
I   t75 l8 \vb20 \vs20 \vh20 [\vh+1 \b\h r \vh+1\h r \vh+1\s\h r \vh+1\h r]4
;          ^^    ^^    ^^     ^^           ^^        ^^          ^^
;       Hi-hat's volume keeps rising
```



### RSS Global Volume (`\V<value>`)

This command will set the global volume affecting RSS channels. Range is 0–63.

```
I   t75 \V63 l4 [\b r \s r \b r8 \b r8 \s r \V50 ]2
;       ^^                                  ^^
;       Sets the global volume, then changes it in the second loop
```

You can also change it relatively by using +/- before the value.

```
I   t75 \V63 l4 [\b r \s r \b r8 \b r8 \s r \V-13 ]2
;       ^^                                  ^^
;       Same sequence as before but with a relative change
```

When this command isn't used, the driver defaults the value to 48.

### RSS Channel Panning (`\<pan><perc>`)

Changes the panning of each rhythm channel individually. `<pan>` represents the panning position as follows:

- `r` = Right
- `l` = Left
- `m` = Center
```
I   t50 l8 [\mc \b\c r \lc\c r \lh\h\s\cp r \h\s r \b\h r \c r \cp\h\s r \h r]4
;           ^^         ^^      ^^
;       Centers the cymbal on the first beat, pans it and the hi-hat to the left for the rest
```

### Using Macros for readability

As you've seen above, the rhythm can get quite messy to sequence manually, so it's good to use macros for making the sequencing process faster and the script more readable.
```
!h  \h\cp   ;   Hi-hat, mutes Cymbal
!H  \lc\c   ;   Cymbal to the left to simulate an open hi-hat
!c  \mc\c   ;   Center cymbal

I   \V63 \lh t50 l8 
I   [!c\b r !H r !h\s r !h\s r \b!h r !H r !h\s r \h r]4

;   Same sequence as the previous, but cleaner
```

## PMD's Rhythm Channel

An alternative way to sequence drums is the PMD rhythm channel. This is a channel that uses instrument commands to sequence drums instead, which by default uses SSGPCM on SSG Channel 3 (`I`) alongside the RSS.

If making a song that uses the `I` channel, be mindful that any notes in the rhythm channel will override it, so make smart use of rests.

One thing to keep in mind is that the volume of the default SSG drums can't be changed except with `#Volumedown` which will affect all SSG channels. The RSS part can be changed with `\V<value>`.

### PMD Rhythm Channel Sequencing

The rhythm channel `K` is used to sequence definitions using `R<value>`, as follows:

```
R0  @1 [c]8
K   R0
```

While a variety of commands are supported in the `K` channel itself, notes and rests are not. The `R` definitions can be made at any point in the script.

### PMD Rhythm Definition Sequencing

To select the instrument, we use `@<value>`. The values associated with each drum by default are as follows:
```
@1	  - Bass Drum
@2	  - Snare Drum 1
@4	  - Low Tom
@8	  - Middle Tom
@16	  - High Tom
@32	  - Rim Shot
@64	  - Snare Drum 2
@128  - Closed Hi-hat
@256  - Open Hi-hat
@512  - Crash Cymbal
@1024 - Ride Cymbal
```

To sequence a note, we insert any note after it. Whatever note you choose won't change the resulting sound.

```
K   [R0]4
R0  l4 @1 c @2 c @1 c8c8 @2 c
```

Because the instrument is defined in binary, you can add those values to play 2 or more RSS channels at the same time. So for example, you can play both the snare and hi-hat by adding `@2 + @128 = @130`.

However, the SSG channel will still only play one of the drums, and the lowest value takes priority.

```
K   [R0]4
R0  l8 @129c @128c @130c @128c @129c c @130c @256c
;      ^^          ^^          ^^      ^^
;   A hi-hat is added to the bass and snare drums on the RSS
;   The SSG only plays the hi-hat when the bass and snare aren't playing.
```

Some commands trigger the same RSS channel using different panning. `@4`, `@8`, and `@16` use the tom, and `@256`, `@512`, and `@1024` use the cymbal. When using 2 of them at the same time, the lowest value will be played on the SSG, and the panning for RSS will correspond to the highest one.

```
K   l2 R0 R1
R0  @768  c  ;256+512, @256 plays on the SSG and RSS uses @512's panning
R1  @1536 c  ;512+1024, @512 plays on the SSG and RSS uses @1024's panning
```

The rhythm commands explained in the first section can also be be used to manually trigger RSS channels. This allows for bypassing number priorities when adding values.

```
K   R0 R1
R0  @129 c @130 c @129 c @130 c     ;Hi-hat doesn't play on the SSG
R1  \vb31\vs31 @128 \bc \sc \bc \sc ;Hi-hat plays on the SSG
```

### Commands Supported by the K/R channels

This is a list of commands that can be used on both PMD rhythm sequence types for drum sequencing.

 - `[ : ]` - Loop
 - `l` - Default Length
 - `L` - Channel Loop
 - `t` - Tempo
 - `C` - Zenlen
 - `T` - Timer

Almost everything else will be ignored or will result in a compile error. There are some additional commands that are only supported by direct use of SSGPCM, which are properly explained in [its own section](./6PCM.md).

_Reminder to hyperlink on final revision_