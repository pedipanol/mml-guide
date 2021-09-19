# pedipanol's guide to basic MML syntax
Hello! I'm Saria and here's another attempt to explain MML script to anyone who's willing to learn! This will cover things similar in the most formats, including PMD, (pp)mck, MDXDRV, gbmc, mgsdrv, MML2VGM.

*SNES MML is different from the rest because it was born from romhacking tools instead being conceived as proper MML formats. So things said here might not be applied there.*

## The Basic Structure

In most MML formats there will be 3 essential parts to its structure:
1. The Header
2. Instrument Macros
3. Sequence

Look at this sample MML made for PMD and try to identify them:
```
#Title		Drive Me Wild
#Composer	Garoad
#Arranger	pedipanol
#Option		/v/c
#Tempo		75
#Volumedown	F18,S5,R15

@  2  7 3
  31 12 0 6 15  0 0  1 0 0
  31 11 0 6 15  0 0  2 0 0
  31 10 0 6 15  0 0  4 0 0
  31  9 0 6 15  0 0  8 0 0

@  3  4 6
  27  4 0 6 15 35 1  3 3 0
  31  7 0 7 15  0 2  1 4 0
  31  7 0 4 14 41 1 14 7 0
  31  8 0 7 15  0 0  4 7 0

ACE	@3 o6 l4 V114
BDF	@2 o7 l4 V108
CD	(3 p1 D-2 r8
EF	(6 p2 D2 r4
ABCD	[[c<bgd8>c<bgd8:f]2g8a8[b-afc8b-afc8:e-]2 f8:g8]2
AB	g8
EF	[c<bgd8>c<bgd8:f]2g8a8[b-afc8b-afc8:e-]2 
```
It's literally in the order that I said, but the truth is that it doesn't need to. Because how a line starts dertermines its function and what you can do in them. To me, separating them clearly makes it easier to udnerstand, but you can put them however you like.

The header lines are started with **\#**, in there you define all sorts of metadata for the song, that will be output in the compiled file. In PMD's case you don't need it for compiling, but in others it may result in an error.

The instrument definitions are started with **\@**, this is the part that varies the most depending on the driver you're using. In PMD's case, it's only used for the FM isntrument parameters, so it reads the next 43 numbers with any kind of spacing as the instrument defined by the first number.

The sequence lines are started with **capitalized letters** (sometimes numbers). Each one refers to a specific channel of the soundchip, so you can combine them to make 2 channels play the same line and things like that, really useful for layering and channel echo (both being done here). But be careful to not use commands the channels don't share or it'll result in an error.

You can also **comment lines**, in most it's done starting the line with **;** but there are exceptions.

Since the Header and Instrument definitions are driver-specific, I'll save a more detailed explanation for them for my specific guides. Time for explaining the basic sequence!

## The Basic Sequence