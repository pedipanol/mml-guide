# FM3Extend

FM Channel 3 in the OPN soundchips has a special mode that allows frequencies to be set independently between the 4 operators, bringing the possibility of extended polyphony and other effects.

You need to know know what you're doing to use this function, as it doesn't work with all instruments. For this example, I'll use the extended polyphony, as it's the most common use for it.

## Extended Polyphony

The most important instrument parameter for this is Algorithm (ALG), which is the first value in the instrument setting after the instrument number.

```
@ 0  4  5
;    ^ It's this one!
 31 24  1  4  1   1 0  1 3 0
 31 14  5  7  2   0 1  4 7 0
 31 20  6  7  3  55 0 15 3 0
 31 14  3  5  2   0 1  1 0 0
```

If you don't know how the FM algorithms work, basically, they arrange the operators in a chain, determining their functions, whether they'll modulate or if they'll output sound, and the order. Here's a diagram of what layout each value represents:

![FM Operator Diagram](./images/fmalg.png)
_made by MovieMovies1_

As you can see, ALG 0 to 3 are a chain of modulators being carried only by operator 4, whereas ALG 4 to 7 have multiple carriers outputting.

In order to get extended poliphony we'll have to use ALG 4 6 and 7. Alg 5 could theoretically be used, but since Operator 1 modulates all of them, it'll get a frequency clash.

As you might have guessed, the caveat to the extended mode is that the more extra polyphony you want, the simpler the sound has to be due to using less slots for modulation. It works like this:

- ALG 4: 1 additional channel, 2-Op synthesis
- ALG 6: 2 additional channels, main one is 2-Op synthesis and the other 2 are sine waves
- ALG 7: 3 additional channels, main one can use the Feedback to change its sound, the other 3 are sine waves.

Because of this it's very rare for people to use more than 1 additional channel, since ALG 4 is more versatile. All the examples here will do so as well.

### Creating Additional Channels

To create additional channels, we use the following command:

```
#FM3Extend  <letter(s)>
```

Any letter aside from the already assigned ones (ABCDEFGHIJKR) can be used for this, including lowercase. For example:

```
#FM3Extend  X ; Channel X will be associated with FM3
```

You can also input up to 3 letters. X, Y, and Z are common letters used for FM3Extend channels.

### Assigning operators to the channels

That was only the first step. Now, we have to assign the operators using the operator flag command `s<operator>`, explained in [Chip Commands](./chipcom.md#operator-flag-soperator).

```
#FM3Extend  X

C   s3      ;Assigns Operator 1 and 2 to Channel C
X   s12     ;Assigns Operator 3 and 4 to Channel X
```

With this, essentially, we have 2 fully fledged 2-Op channels to work with. The only 2 things to keep in mind are:


- Using stereo commands will affect all the FM3Extend channels.
- Changing instruments will only affect the operators set up by the `s` command, but the Feedback and ALG values will be overwritten.

## Additional Commands

Here, I'll explain a couple commands that work on FM channel 3.

### Operator Detune (`sd<operator>,<value>`)

Changes the tuning of individual operators on channel 3.

While with the FM3Extend channels, you could use the common detune commands, this allows you to use this kind of detune without creating additional channels.
```
@ 2  4  0
 31  0  0  0  0  20 1  4 7 0
 12  0  0  7  0   0 0  8 7 0
 31  0  0  0  0  20 1  4 7 0
 12  0  0  7  0   0 0  8 7 0

C	@2 l8 q3 cgf16e16fgag4
C   sd12,-2  cgf16e16fgag4 ;Detunes operators 3 and 4
```

### Pitch Software LFO

By default, the pitch software envelope will affect all the operators on FM Channel 3. However, only in this channel, you can use the LFO slot command `MM<operator>` to affect an individual operator.
```
@0 0 0
 31 0 0 7 0 127 0 0 0 0
 31 0 0 7 0  27 0 0 0 0
 31 0 0 7 0  32 0 2 0 0
 31 0 0 7 0   0 0 1 0 0 

C	@0 o5 sd2,-2500     ;Lowers the frequency of Op 2 to be come a vibrato
C   MM2 MP+13 d1&1&1&1  ;Pitch LFO rises Op2's frequency for a crazy effect
``` 