# PPSDRV (PPS)

This is the most complex PCM mode to work with and the most limited in many ways. I also don't fully understand it _that well_ compared to everything else in this guide.

While FMPMD and MML2VGM provide some form of emulation, it's not at all accurate. The only way to get accurate playback outside a the real PC98 machine is with [NekoProject II fmgen](1Setup.md\#nekoproject-ii-fmgen-windows) using fmgen.dll .


PPSDRV is a PCM driver for the SSG, which allows 4-bit 16000khz PCM playback on the SSG Channel 3. It can also play 2 samples simultanelously, but at a lower quality.

This is the only PCM mode that will work on a PC98 with a 26 soundboard (which has a YM2203 instead of a YM2608), so using it can be good for compatibility.

There's a total size limit of 58kb for the samples after conversion.

## Making a PPS File

_(Make sure to do everything in the main [PCM](6PCM.md\#handling-pcm) page before proceeding with this guide)_

### Preparing the Samples

First you need to get your samples ready. Open your samples in an audio editor, then:

- Resample them

    The recommended sample rate is 16000hz. It allows for some fluctuation as we'll see, but to avoid trouble is best to stick the the default sequence by the driver.

- If it's a stereo sample, convert it to mono

    The converter doesn't support stereo samples.

- Export them as 16-bit unsigned RAW.
    
    In Audacity, this is done by going to "Export Audio...", choosing "Other uncompressed files" at the time of export, and then choosing "16-bit unsigned" and RAW (headerless).

I also recommend putting a brief moment of silence after the sample finishes to avoid potential clicks in the converted audio.

Save the samples individually in the workspace folder.

### Sample Conversion

Now we'll use PCMCONV to get them to the PPSDRV format.

In DOS, go to your workspace folder and use the following command:

```
pcmconv /w <input>.raw /n <output>.spc
```

`/w` specifies the (input) file as a RAW 16-bit little-endian file, `/n` specifies the (output) file as 4-bit signed.

If for some reason you wish to, you can also specify the sample rate of the samples after each filename:

```
pcmconv /v guitar.raw 16000 /a guitar.spb 16000
```

### Packing it up in a PPS Bank

As the PPS needs some variables to be specified, it uses an external text file for defining its samples. So create a .txt document in the folder, change its extension to .cfg and open it in a text editor like notepad.

The text structure is as follows:
```
#<output filename>.pps

<filename>.spc  <slot>  <pitch>  <volume>
<filename>.spc  <slot>  <pitch>  <volume>
[...]
```

In the first line you specify the name of the output .PPS file. Then the specifics of each sample.

`<filename>` corresponds to the SPC file outputted by PCMCONV before. You can also use `=<slot>` instead to use a previously loaded sample.

`<slot>` sets which slot in the PPS file the sample will take, which is relevant when calling it. 0 through 13 can be choosen

`<pitch>` changes the playback speed of the sample, changing its pitch. The allowed range is from -224 to +30.

`<volume>` will reduce the sample's volume. This is useful if the end result is too loud, but it'll reduce the output quality.

Additionally, comments with `;` are allowed and you can space at will as long as the values are properly defined.

An example of the final file is:
```
#TEST.PPS

;Filename		Slot	Pitch	Volume
kick.spc		0		0		0		
snare.spc       1       0       0
guita.spc       2       0       0
=2              3       -56     0       ;plays guita.spc at a lower pitch
```

Once everything is defined, to create a .PPS file you simply run

```
makepps <filename>.cfg
```

## Driver Load Order

PMD uses the same sequencing format as the rhythm channel, and because of this, in a real machine or emulator, a specific driver load order is required:

```
pdr
ppsdrv
pmd/pmdb2/pmd86 etc.
```

PDR and PPSDRV can use additional commands but those can be addressed in the MML itself and it's better if done so.

If you're working on real hardware and you have a slow one, try adding `/l` so it plays at the low frequency mode, as it might not support the high frequency mode.

## Sequence and Commands

Compared to other types of PCM supported by PMD, PPSDRV is quite unique, as it's fixed to a pitch and doesn't allow sample looping.

It uses the rhythm channel (`K`) for sequencing and behaves the same but with some additional capabilities in comparison. So it's important to read about it [here](5Rhythm.md\#pmds-rhythm-channel) if you have not yet.

### Selecting Samples

The slot you associated in the .cfg file will correspond to the instrument value as follows:

|Sample Slot|Instrument Number|
|---|---|
|0|@1|
|1|@2|
|2|@4|
|3|@8|
|4|@16|
|5|@32|
|6|@64|
|7|@128|
|8|@256|
|9|@512|
|10|@1024|
|11|@2048|
|12|@4096|
|13|@8192|

So for example:

```
K   R0
R0  @512 c    ;Plays the sample in slot 9
```

When specifying the Double pcm mode with `*0` can also play 2 samples at the same time by adding 2 of them together:

```
K   *0 R0 R1
R0  @2 c @4 c ;Plays slot 1 then 2 
R1  @6 c      ;Plays both together
```

The only thing you need to keep in mind is that the same sample cannot play at 2 different sample rates at the same time.(?)

### Volume Change

You can change the volume with `v<value>`, the range is 0-15.
```
K   R0
R0  @1 v15 c v8 c ;Plays slot 0 at full volume then lower
```

Note that you're lowering the bitrate of your sample playback when doing this, so their quality will worsen the lower the volume is.

