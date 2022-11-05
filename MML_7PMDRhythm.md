# The Rhythm Channels

This page explains the 2 Rhythm methods PMD supports: the OPNA's Rhythm Sound Source (RSS) channels and PMD's Rhythm Channel `K`

## Rhythm Sound Source Commands

In PMD, the RSS can be controlled from any channel by using the trigger command `\<perc>`:

`<perc>` corresponds to which rhythm channel will be used, as follows:

- `b` - Bass Drum
- `s` - Snare
- `t` - Tom
- `h` - Hihat
- `c` - Cymbal
- `i` - Rim Shot 

Those commands are set to be triggered in the next note or rest:

```
I   t75 l4 \b r \s r \b r8 \b r8 \s r
```

This way you can trigger multiple drums at the same time in a single channel by simply stacking the commands:

```
I   t75 l8 \b\h r \h r \s\h r \h r \b\h r \b\h r8 \h\s r \c r
```

To halt an ongoing sound in the next note simply add a `p` after it.

```
I   t50 [\b\h r12 \h r24 \c r8 \h\b\s r12 \h r24 \c r12 \cp\h r24]4
;                       ^^                      ^^      ^^
;       The first cymbal doesn't get halted, the second gets
```

Before moving on, keep in mind that "triggered in the next note" also refer to loops, if you put a rhythm command at the end of a loop it'll play on the first note of it and the next after.

```
I   t50 [\b r \s r \h]2 \t r
;           ^      ^^      ^
; Hihat plays both at the first and next note
```

### Rhythm Channel Volume (`\v<perc><value>`)

This changes the volume of the specified channel individually. The value range is 0-31.

```
I   t75 l8 \vb20 \vs20 [\vh20 \b\h r \vh5\h r \vh20\s\h r \vh5\h r]4
;          ^^    ^^     ^^           ^^       ^^          ^^
;       Sets the volume for bass and snare drum, lowers the hihat's movies on off-beats
```

You can also change it relatively by using +/- before the value.
```
I   t75 l8 \vb20 \vs20 \vh20 [\vh+1 \b\h r \vh+1\h r \vh+1\s\h r \vh+1\h r]4
;          ^^    ^^     ^^    ^^           ^^        ^^          ^^
;       Hihat's volume keeps rising
```



### Rhythm Global Volume ( `\V<value>`)

This command will set the global volume affecting RSS channels. Range is 0-63.

```
I   t75 \V63 l4 [\b r \s r \b r8 \b r8 \s r \V50 ]2
;       ^^                                  ^^
;       Sets the global volume then changes it in the second loop
```

You can also change it relatively by using +/- before the value.

```
I   t75 \V63 l4 [\b r \s r \b r8 \b r8 \s r \V-13 ]2
;       ^^                                  ^^
;       Same sequence as before but with a relative change
```

When this command isn't used, the driver defaults the value to 48.

### Rhythm Channel Panning ( `\<pan><perc>`)

Changes the panning of each rhythm channel individually. `<pan>` represents the panning position as following:

- `r` = Right
- `l` = Left
- `m` = Center
```
I   t50 l8 [\mc \b\c r \lc\c r \lh\h\s\cp r \h\s r \b\h r \c r \cp\h\s r \h r]4
;           ^^         ^^      ^^
;       Centers the cymbal on the first beat, pans it and the hihat to the left for the rest
```

### Using Macros for readability

As you've seen above, the rhythm can get quite messy to sequence manually, so it's good to use macros for making the sequencing process faster and the script more readable.
```
!h  \h\cp   ;   Hihat, mutes Cymbal
!H  \lc\c   ;   Cymbal to the left to simulate an open hihat
!c  \mc\c   ;   Center cymbal

I   \V63 \lh t50 l8 
I   [!c\b r !H r !h\s r !h\s r \b!h r !H r !h\s r \h r]4

;   Same sequence as the previous, but cleaner
```

## PMD's Rhythm Channel

An alternative way to sequence drums is the rhythm channel. This is a channel that uses instrument commands to sequence drums instead, which by default uses the SSG Channel 3 (`I`) alongside the RSS.

If making a song that uses the `I` channel, be mindful that any notes in the rhythm channel will overwrite it, so make smart use of rests.

One thing to keep in mind is that the volume of the default SSG drums can't be changed except for `#Volumedown` which will affect all SSG channels. The RSS part can be changed with `\V<value>`.

### Rhythm Channel Sequencing

The rhythm channel `K` is used to sequence macros defined by `R<value>`, as follows:

```
R0  @1 [c]8
K   R0
```

While a variety of commands are supported in the `K` channel itself, notes and rests are not. The `R` macros can be defined at any point in the script.

### Rhythm Macro Sequencing

To select the instrument we use `@<value>`. The values associated with each drum by default are as following:
```
@1	  - Bass Drum
@2	  - Snare Drum 1
@4	  - Low Tom
@8	  - Middle Tom
@16	  - High Tom
@32	  - Rim Shot
@64	  - Snare Drum 2
@128  - Closed Hihat
@256  - Open Hihat
@512  - Crash Cymbal
@1024 - Ride Cymbal
```

To sequence a note we insert any note after it. whatever note you choose won't change the resulting sound.

```
K   [R0]4
R0  l4 @1 c @2 c @1 c8c8 @2 c
```

Because the definition is done in binary, you can add those values to play 2 or more Rhythm channels at the same time. So for example you can play both the snare and hihat by adding `@2 + @128 = @130`.

However, the SSG channel will still only play one of the drums, and the lowest value takes priority.

```
K   [R0]4
R0  l8 @129c @128c @130c @128c @129c c @130c @256c
;      ^^          ^^          ^^      ^^
;   A hihat is added to the bass and snare drum on the RSS
;   The SSG only plays the hihat when the bass and snare aren't playing.
```

Some commands trigger the same RSS channel using different panning. `@4` `@8` `@16` use the Tom and `@256` `@512` `@1024` use the Cymbal. When using 2 of them at the same time, the lower value will be played in the SSG and the panning will correspond to the higher one.

```
K   l2 R0 R1
R0  @768  c  ;256+512, @256 plays on the SSG and RSS uses @512's panning
R1  @1536 c  ;512+1024, @512 plays on the SSG and RSS uses @1024's panning
```

The rhythm commands explained in the first section can also be be used to manually trigger RSS channels. This allows for bypassing the lower number priority when adding values.

```
K   R0 R1
R0  @129 c @130 c @129 c @130 c     ;Hihat doesn't play on the SSG
R1  \vb31\vs31 @128 \bc \sc \bc \sc ;Hihat plays on the SSG
```

### Commands Supported by the K/R channels

This is a list of commands that can be used on both rhythm channels for drum sequencing.

 - `[ : ]` - Loop
 - `l` - Default Length
 - `L` - Channel Loop
 - `t` - Tempo
 - `C` - Zenlen
 - `T` - Timer

Almost everything else will be ignored or will result in a compile error. There are some additional commands that are only supported by SSGPCM, which are properly explained in its own section.

_Reminder to hyperlink on final revision_