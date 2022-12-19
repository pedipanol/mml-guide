# Handling PCM

PMD has extensive PCM capabilities beyond just the ADPCM of the OPNA. It has a difficult setup process for getting the PCM files, but once that's done, it's pretty simple to use.

## The four types of PCM

The PCM types PMD has are:

- ADPCM: using OPNA's internal ADPCM channel
- P86DRV (86PCM): uses the PC-98's 86 soundboard's PCM as an alternative to the ADPCM
- PPZ8: uses the PC-98's 86 soundboard's PCM to generate up to 8 PCM channels.
- PPSDRV (SSGPCM): uses SSG channel 3 to generate up to 2 PCM channels.

All of them have different capabilities, and whether it's possibile to use them on real machines will depend on the soundboard setup. This is the reason many PMD soundtracks that use ADPCM will have an 86PCM version.

That being said, most PC-98 audio aficionados will have an 86 soundboard with a Chibi-Oto, enabling the use of all the 4 types.

## Making a PCM conversion workspace

To make your own PCM samplebanks, you'll need to set up a workspace folder on DOS. For this purpose, [DOSBox-X](./setup.md#dosbox-x-windows-mac-linux) is recommended, as it's easier to handle files.

Once the DOS folder is set up, get the following files:

1. Download PMD Ver.4.8o and from [KAJA's website](https://sites.google.com/site/kajapon/pmd)
    
    Extract the following files into the folder:
    - PMDPCM.EXE
    - PMDPCM86.EXE
    - MAKEPPS.EXE
    - P86DRV.COM
    - PDR.COM
    - PPSDRV.COM
    - PPC2PVI.COM
    - PMDPCM.DOC (optional)
    - MAKEPPS.DOC (optional)

2. If you haven't, download PMD Ver.4.8s from [KAJA's website](https://sites.google.com/site/kajapon/pmd)
    
    Extract or copy the following files into the folder:
    - PMDB2.COM
    - PMD86.COM
    - PMDPPZ.COM

3. Download PCMCONV from [vector.jp](https://www.vector.co.jp/soft/dl/dos/art/se004506.html) (click the green "Go to the download page" then the "Download Now" button)

    Extract the following files:
    - PCMCONV.COM
    - PCMCONV.DOC (optional)

4. Download PZIUTY [here](https://aosoft.jp/dlfiles/pziuty10b.zip).

    Extract all files into the folder.

The optional .DOCs are good to have in case you want to troubleshoot something that might not be properly explained in here. Unfortunately, it's in japanese, so you'll need to use a translation tool if you don't understand.

You'll also need an audio editor that can change sample rates and export unsigned 16-bit RAW audio, depending on the PCM type. For this tutorial, I'll use Audacity.

## ADPCM (PPC)

The YM2608 soundchip has a 4-bit ADPCM channel, which has 256kB of RAM available for storing samples. This is the main limitation to have in mind when creating sample banks for ADPCM, as the PPC file itself cannot go above 256kB as well.

The lack of this RAM is the reason the 86 soundboards don't natively support this channel. Thankfully, it can be modded with with the Chibi-oto add on, which adds the missing RAM and gives full functionality to the channel.

### Making a PPC File

#### Preparing the Samples

First, you need to get your samples ready. Open your samples in an audio editor, then:

- Resample them to below 55kHz

    The max sample rate supported by the OPNA is 55kHz, but going this high will not only increase the filesize dramatically, but also limit the pitch range.
    
    A sample will play at 16kHz at `o5g`. Given that the maximum sample rate it 55kHz, this is a fair amount of upwards range.
    
    Because samples play at 16kHz at `o5g`, it is recommended to make your samples 16kHz (and at that pitch if that matters). This will ensure the audio sounds the same when played at `o5g`.

- Apply a filter for cymbal-type instruments

    Cymbals and hihats tend to generate a lot of noise during ADPCM conversion, so I recommend applying a filter to avoid it happening.

- If it's a stereo sample, convert it to mono

    The converter doesn't support stereo samples. It will still accept them, but the sample channels will be stacked or interlaced.

- Export them as 16-bit unsigned RAW.
    
    In Audacity, this is done by going to "Export Audio...", choosing "Other uncompressed files" at the time of export, and then choosing "16-bit unsigned" and RAW (headerless).


I also recommend putting a brief moment of silence after the sample finishes to avoid potential clicks in the converted audio.

Save the samples individually in the workspace folder.

#### Sample Conversion

Now we'll use PCMCONV to get them to the ADPCM format.

In DOS, go to your workspace folder and use the following command:

```
pcmconv /w <input>.raw /a <output>.spb
```

`/w` specifies the (input) file as a RAW 16-bit little-endian file, `/a` specifies the (output) file as ADPCM.

If for some reason you wish to, you can also specify the sample rate of the samples after each filename:

```
pcmconv /v guitar.raw 16000 /a guitar.spb 16000
```

#### Packing it up in a PPC Bank

Now to wrap up the package.

1. Run the DOS driver to start PMD with ADPCM support:

    ```
    pmdb2
    ```


2. Run the following command to clear the ADPCM RAM:
    ```
    pmdpcm /c
    ```

3. For every .spb sample you want to include in the file, run:
    ```
    pmdpcm <id> <filename>.spb
    ```
    In which `<id>` corresponds to the instrument number to be called with the `@` command. Range is 0–255.

    This will load the sample into RAM and associate its addresses to its ID.

4. Once you're finished with all samples, wrap it up with:
    ```
    pmdpcm /s <filename>.ppc
    ```

You can rebuild the file by running from step 2 onwards. If you want to add a new sample to the bank, simply load the PPC file:
```
pmdpcm /lr <filename>.ppc
```
Then execute step 3 for every new sample in unclaimed slots, then step 4.


### Sequence and Commands

In the MML, to load a PPC file when playing the song, add the following header:

```
#PPCFile    <filename>.PPC
```

The PPC file needs to always be in the same folder as the .MML or .M file for the song to be played properly. Some players won't play the song at all if the intended sample bank is not present or pre-loaded in memory.

To sequence the ADPCM, use the `J` channel.

#### Set instrument (`@<value>`)

To choose the PCM isntrument, use `@`, in which the value corresponds to the sample ID in the PPC. This is will make the sample play as-is once when a note is played.

#### Set looped instrument (`@<value>,<start>,<end>,<release>`)

By adding values after setting the instrument, PMD can loop the samples from specific loop points.

However, ADPCM's case is a complicated process because of its encoding format.

Firstly, the step count doesn't correspond to the same as the original sample. OPNA loads ADPCM samples in 32-bit chunks, so the count for this command is every 32-bits, or every 8 samples. If a sample is planned to loop, ensure that the loop points are multiples of 8, and divide accordingly. Also, `<end>` refers to the last chunk played for the loop, so be sure to not accidentally specify the chunk *after* the loop!

Secondly, ADPCM doesn't natively support loop points, instead looping the entire sample. PMD circumvents this by changing the sample start offset just after beginning sample playback, but due to how the sample data is interpreted, this changes the actual waveform output, making it dificult to make a perfectly looping sample and sometimes lowering the volume. The technical reason is because the conversion from 4-bit ADPCM to an audio signal for a given sample depends on the previous output.

#### Set volume envelope

You can set a volume envelope with the `E` command and it works as [specified before in the guide](./chipcom.md#volume-envelopes).

#### Notes

As mentioned before, if the sample is encoded as 16kHz, `o5g` will play the same as the source file.

If you're using a pitched sample, and its pitch is any note other than a G, it's recommended to use a global transposition `_M<value>` and/or global detune command `DD<value>` to pitch it accordingly.

Making a macro is advised if using multiple samples with different base pitches and volumes.

## P86DRV (P86)

PMD's internal 86PCM driver replaces the ADPCM channel with a higher quality 8-bit sample playback. Its stereo capabilities also adds a new functionality to panning.

There's no size limit.

### Making a P86 File

#### Preparing the Samples

First you need to get your samples ready. Open your samples in an audio editor, then:

- Resample them

    As PMD tries to make 86PCM compatible with ADPCM, it'll have many similarities with it, including its recommended sample rates and whatnot.

    The only documented sample rate is 16504Hz, which will play at `o5g`. But since some audio editors don't support it, I recommend exporting to the closest one upwards. I use 22050Hz.

    The highest supported sample rate is not documented, and it can go above the ADPCM's.

- If it's a stereo sample, convert it to mono

    The converter doesn't support stereo samples.

- Export them as 16-bit unsigned RAW.
    
    In Audacity, this is done by going to "Export Audio...", choosing "Other uncompressed files" at the time of export, and then choosing "16-bit unsigned" and RAW (headerless).

I also recommend putting a brief moment of silence after the sample finishes to avoid potential clicks in the converted audio.

Save the samples individually in the workspace folder.

#### Sample Conversion

Now we'll use PCMCONV to get them to the 86PCM format.

In DOS, go to your workspace folder and use the following command:

```
pcmconv /w <input>.raw /b <output>.pc8
```

`/w` specifies the (input) file as a RAW 16-bit little-endian file, `/b` specifies the (output) file as 8-bit signed.

Since PMD uses a really specific frequency for o5g. If you're using a pitched sample and the input file is tuned, add the frequency to the conversion, as follows:

```
pcmconv /v <input>.raw 22050 /b <input>.pc8 16504
```

#### Packing it up in a P86 Bank

Now to wrap up the package.

1. Run the following 2 DOS drivers to start PMD with 86PCM support and enable use of 86PCM:

    ```
    pmd86
    p86drv
    ```


2. Run the following command to clear the sample memory in RAM:
    ```
    pmdpcm86 /c
    ```

3. For every .pc8 sample you want to include in the file, run:
    ```
    pmdpcm <id> <filename>.pc8
    ```
    In which `<id>` corresponds to the instrument number to be called with the `@` command. Range is 0–255.

    This will load the sample into RAM and associate its addresses to its ID.

4. Once you're finished with all samples, wrap it up with:
    ```
    pmdpcm /s <filename>.p86
    ```

You can rebuild the file by running from step 2 onwards. If you want to add a new sample to the bank, simply load the P86 file:
```
pmdpcm /lr <filename>.p86
```
Then execute step 3 for every new sample in unclaimed slots, then step 4.

### Sequence and Commands

In the MML, to load a P86 file when playing the song, add the following header:

```
#PCMFile    <filename>.P86
```

The P86 file needs to always be in the same folder as the .MML or .M file for the song to be played properly. Some players won't play the song at all if the intended sample bank is not present or pre-loaded in memory.

To sequence the 86PCM, use the `J` channel.

> You can also set PPZ8's PZI files in `#PCMFile` for higher quality samples in the `J` channel, but for that, you'll need to load PMDPPZ instead of PMD86, and many Windows players don't support it.

#### Set instrument (`@<value>`)

To choose the PCM isntrument, use `@`, in which the value corresponds to the sample ID in the P86. This is will make the sample play as-is once when a note is played.

#### Set looped instrument (`@<value>,<start>,<end>,<release>`)

By adding values after setting the instrument, PMD can loop the samples from specific loop points.

To get the correct points, I search for them in sample units in audacity, and then divide the value by 32.

You can change the value behavior to be similar to ADPCM's by adding `/s` to the `#Option` header.

#### Set volume envelope

You can set a volume envelope with the `E` command and it works as [specified before in the guide](./chipcom.md#volume-envelopes).

#### Notes

As mentioned before, if the sample is encoded as 16504Hz, `o5g` will play the same as the source file.

If you're using a pitched sample and its pitch is any note other than a G, it's recommended to use a global transposition `_M<value>` and/or global detune command `DD<value>` to pitch it accordingly.

Making a macro is advised if using multiple samples with different base pitches and volumes.

#### Portamento and Pitch LFO

The portamento notation (`{}`) is not supported in the 86PCM. Using it will result in only the last note being played.
```
J   @1 {cg}4        ;Only g is played
```

Additionally, any pitch LFO command will be ignored.

#### Panning

While the ADPCM on the OPNA has hardpanning, the 86PCM has full stereo control so PMD has an additional panning command allowing for the extended range, with `px<value>`

The value range is -127 to 127, in which the negative values pan torwards the right and the positive values pan to the left.

PMD can also do a reverse phase effect, which results in a "surround" sound compared to the mono original sound. To use it you just add a 1 after the panning value:

```
J   @1 c2 px0,1 c2
```

This is a good effect if you want to the samples to sound fuller, however it might render the PCM channel silent if listening to the song in a mono speaker or environment, so keep that in mind.

The normal `p<value>` pan command also works in PMD86, and it's also hardpanned. However it gains an additional value in `p4` which allows for phase reversing.

## PPSDRV (PPS)

This is the most complex PCM mode to work with and the most limited in many ways. I also don't fully understand it _that well_ compared to everything else in this guide.

While FMPMD and MML2VGM provide some form of emulation, it's not at all accurate. The only way to get accurate playback outside a the real PC98 machine is with [NekoProject II fmgen](./setup.md#nekoproject-ii-fmgen-windows) using fmgen.dll .


PPSDRV is a PCM driver for the SSG, which allows 4-bit 16000khz PCM playback on the SSG Channel 3. It can also play 2 samples simultanelously, but at a lower quality.

This is the only PCM mode that will work on a PC98 with a 26 soundboard (which has a YM2203 instead of a YM2608), so using it can be good for compatibility.

There's a total size limit of 58kb for the samples after conversion.

### Making a PPS File

#### Preparing the Samples

First you need to get your samples ready. Open your samples in an audio editor, then:

- Resample them

    The recommended sample rate is 16000hz. It allows for some fluctuation as we'll see, but to avoid trouble is best to stick the the default sequence by the driver.

- If it's a stereo sample, convert it to mono

    The converter doesn't support stereo samples.

- Export them as 16-bit unsigned RAW.
    
    In Audacity, this is done by going to "Export Audio...", choosing "Other uncompressed files" at the time of export, and then choosing "16-bit unsigned" and RAW (headerless).

I also recommend putting a brief moment of silence after the sample finishes to avoid potential clicks in the converted audio.

Save the samples individually in the workspace folder.

#### Sample Conversion

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

#### Packing it up in a PPS Bank

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

### Driver Load Order

PMD uses the same sequencing format as the rhythm channel, and because of this, in a real machine or emulator, a specific driver load order is required:

```
pdr
ppsdrv
pmd/pmdb2/pmd86 etc.
```

PDR and PPSDRV can use additional commands but those can be addressed in the MML itself and it's better if done so.

If you're working on real hardware and you have a slow one, try adding `/l` so it plays at the low frequency mode, as it might not support the high frequency mode.

### Sequence and Commands

Compared to other types of PCM supported by PMD, PPSDRV is quite unique, as it's fixed to a pitch and doesn't allow sample looping.

It uses the rhythm channel (`K`) for sequencing and behaves the same but with some additional capabilities in comparison. So it's important to read about it [here](./rhythm.md#pmds-rhythm-channel) if you have not yet.

#### Selecting Samples

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

#### Volume Change

You can change the volume with `v<value>`, the range is 0-15.
```
K   R0
R0  @1 v15 c v8 c ;Plays slot 0 at full volume then lower
```

Note that you're lowering the bitrate of your sample playback when doing this, so their quality will worsen the lower the volume is.

## PPZ8 (PVI,PZI)

PPZ8 is a driver that synthesizes up to 8 PCM channels using the 86PCM, with soft panning possibilities and no memory limit aside from the user's PC98 setup.

It supports 2 kinds of PCM banks: .PVI and .PZI

### Making a PVI File

.PVI is an ADPCM sample bank format which was used in FMP, both for its ADPCM playback and also the original PPZ driver, which was similar to PPS but allowed for PCM synthesis in all 3 SSG channels.

Being ADPCM means it was easy for KAJA to make a converter from the PPC banks, and the format is even supported by PMDB2. So the simplest way to make the PVI banks is to follow the [Making a PPC File](#making-a-ppc-file) guide then run the following program:

```
PPC2PVI <filename>.PPC
```

And it should output a file with the same name and the .PVI extension.

The problem with this method is that you lose on having the loop information in the PCM file itself, and the sample quality is significantly lower.

### Making a PZI File

#### Preparing the Samples

First you need to get your samples ready. Open your samples in an audio editor, then:

- Resample them if they're above 65533Hz

    The 86 Sounboard's playback rate is 44100, and PPZ will always play the file with the specified sample rate at o5c so it shouldn't be something to worry about.

- If it's a stereo sample, convert it to mono

    The converter doesn't support stereo samples.

- Export them as 16-bit unsigned RAW.
    
    In Audacity, this is done by going to "Export Audio...", choosing "Other uncompressed files" at the time of export, and then choosing "16-bit unsigned" and RAW (headerless).

I also recommend putting a brief moment of silence after the sample finishes to avoid potential clicks in the converted audio.

Save the samples individually in the workspace folder.

#### Sample Conversion

Now we'll use PCMCONV to get them to the correct format.

In DOS, go to your workspace folder and use the following command:

```
pcmconv /w <input>.raw /b <output>.pc8
```

`/w` specifies the (input) file as a RAW 16-bit little-endian file, `/b` specifies the (output) file as 8-bit signed.

If for some reason you want to specify the sample rates you can do so after the file name:

```
pcmconv /v <input>.raw 44100 /b <input>.pc8 44100
```

#### Packing it up in a PZI Bank

TAN-Y's PZIUTY tool uses an external text file for selecting and specifying the information of the samples. Create a text file in the DOS folder and open it in an editor.

The components of the file are as following:

```
#<PZI filename>
#<sample name>.pc8,<rate>,<loop start>,<loop end>
#<sample name>.pc8,<rate>,<loop start>,<loop end>
[...]
```

Spacing is not allowed and commas are needed between each value.

`<PZI filename>` is the name of the output file. You can include the extension or leave it without and it'll be added automatically.

`<sample name>` is the name of the sample file. Its position/instrument number is associated by the order it was inputted, from 1-127.

`<rate>` is the sample rate of the sample. Accepted range is 0-65533. It can be ommited, but PZIUTY will assume its base rate at 16504hz for .PC8 samples.

`<loop start>` and `<loop end>` are the loop positions of the sample, same as displayed on audacity when displaying audio position by samples. It can be omitted.

Save the file and change its extension to .TFI then run the following command:

```
pziuty l <filename>.tfi
```

And it should output a .PZI file.

### Sequence and Commands

To enable use of PPZ8 in your MML and associate the channels, use the following header:

```
#PPZExtend  <letter(s)>
```

Any letter aside from the already assigned ones (ABCDEFGHIJKR) can be used for this, including lowercase. For example:

```
#PPZExtend  abc ; Channel a b and c will be used for PPZ8
```

PPZ8 supports up to 8 channels so you can input up to 8 letters. The lowercase a, b, c, d, e, f , g and h are the most common letters used for the PPZ8 channels.

To link your track to the .PZI file you're using, use the following header:

```
#PPZFile    <filename>.PZI
```

#### Set instrument (`@<value>`)

To choose the PCM isntrument, use `@`, in which the value corresponds to the line order in the .LFI file. This is will make the sample play as-is once when a note is played.

#### Set looped instrument (`@<value>,<start>,<end>`)

While the loop information can be contained in the PZI file itself, you can also specify it in the MML sequence.

However there's a limited range from -32768 (counting from the end of the sample) to +32767, so for longer samples it's recommended to specify the loop information in the file itself.

#### Set volume envelope

You can set a volume envelope with the `E` command and it works as [specified before in the guide](./chipcom.md#volume-envelopes).

#### Notes

As mentioned before, PPZ will always use the specified sample rate in the PZI file to play the note at `o5c`.

If you're using a pitched sample and its pitch is any note other than a C, it's recommended to use a global transposition `_M<value>` and/or global detune command `DD<value>` to pitch it accordingly.

Making a macro is advised if using multiple samples with different base pitches and volumes.

#### Panning

While the ADPCM on the OPNA has hardpanning, the 86 Soundboard's PCM has full stereo control so like with PMD86, PPZ has an additional panning command allowing for the extended range, with `px<value>`

The value range is -4 to +4, in which the negative values pan torwards the left and the positive values pan to the right.

The normal `p<value>` pan command also works in PPZ, but it's also hardpanned.
