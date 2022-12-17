# The Basic Structure

There are 4 distinct elements in a MML structure which are distinguished by how are they started, which totally depends on the driver and it's not universal. For easier explanation I'll use this PMD template as an example:

```
#Title		Song Name :D
#Composer	me :D
#Arranger	me too :D
#Filename	.M
#Tempo		60

@000 004 005
 031 001 001 001 005 026 000 001 002 000
 031 004 003 008 010 000 000 000 003 000
 031 001 001 001 005 020 000 001 006 000
 031 004 003 008 010 004 000 000 005 000

!a cdefgab>c

A @0D0 p3 v11 o5 l4 q3
A cdefgab>c !a
```

## Header
In the example, the lines starting with `#`.

This will contain **metadata** about the song, as well as compilation instructions. In here, you may define a song's title, insert a comment that will be displayed in the compiled file, set musical details such as the main tempo, etc.

## Instrument Definition/Instrument Macros
In the example, the string of numbers starting with `@`.

This will create the instruments and tables to be called with their respective commands. Oftentimes, there'll only be one type of definition, but particularly for a soundchip with PSG, it's common to have different macro types for different aspects.

Some drivers or channels won't need this setting for a simple sequence, as they support a blank instrument or might have some prebuilt ones. But more often than not, you need to have an instrument definition for every instrument you use, or else it might result in a compilation error or mute the current channel, so it's important to keep it in mind.

## Sequence Macros
In the example, the line starting with `!`

These are sequences you can call with their respective commands. It's useful for saving time and typing, sometimes even minimizing data. Its utilities are endless.

## Channels and Sequence
In the example, the lines starting with `A`.

The sequence and channel addressing can take many forms depending on the MML format.

For a lot of them it's about using a letter or character at the beginning of the line to assign a channel to the sequence that follows in the same line. For others it's not constrained to the same line, but everything until the next channel assignment are considered a sequence of the current channel.

At any rate, the sequence is the core of the MML.