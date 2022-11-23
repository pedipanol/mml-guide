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

To make your own PCM samplebanks, you'll need to set up a workspace folder on DOS. For this purpose, [DOSBox-X](./1Setup.md#dosbox-x-windows-mac-linux) is recommended, as it's easier to handle files.

Once the DOS folder is set up, get the following files:

1. Download PMD Ver.4.8o and from [KAJA's website](https://sites.google.com/site/kajapon/pmd)
    
    Extract the following files into the folder:
    - PMDPCM.EXE
    - PMDPCM86.EXE
    - MAKEPPS.EXE
    - P86DRV.COM
    - PMDPCM.DOC (optional)
    - MAKEPPS.DOC (optional)

2. If you haven't, download PMD Ver.4.8s from [KAJA's website](https://sites.google.com/site/kajapon/pmd)
    
    Extract or copy the following files into the folder:
    - PMDB2.COM
    - PMD86.COM

3. Download PCMCONV from [vector.jp](https://www.vector.co.jp/soft/dl/dos/art/se004506.html) (click the green "Go to the download page" then the "Download Now" button)

    Extract the following files:
    - PCMCONV.COM
    - PCMCONV.DOC (optional)

The optional .DOCs are good to have in case you want to troubleshoot something that might not be properly explained in here. Unfortunately, it's in japanese, so you'll need to use a translation tool if you don't understand.

You'll also need an audio editor that can change sample rates and export unsigned 8-bit or 16-bit audio, depending on the PCM type. For this tutorial, I'll use Audacity, but I only recommend using versions up to 2.4.2 due to data colection being implemented from 3.0 onwards. You can get it from [here](https://www.fosshub.com/Audacity-old.html).

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

#### Notes

As mentioned before, if the sample is encoded as 16kHz, `o5g` will play the same as the source file.

If you're using a pitched sample, and its pitch is any note other than a G, it's recommended to use a global transposition `_M<value>` and/or global detune command `DD<value>` to pitch it accordingly.

Making a macro is advised if using multiple samples with different base pitches and volumes.

## P86DRV (P86)

PMD's internal 86PCM driver replaces the ADPCM channel with a higher quality 8-bit sample playback. Its stereo capabilities also adds a new functionality to panning.

There's no size limit.

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

`/w` specifies the (input) file ss a RAW 16-bit little-endian file, `/b` specifies the (output) file as 8-bit signed.

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

#### Notes

As mentioned before, if the sample is encoded as 16504Hz, `o5g` will play the same as the source file.

If you're using a pitched sample and its pitch is any note other than a G, it's recommended to use a global transposition `_M<value>` and/or global detune command `DD<value>` to pitch it accordingly.

Making a macro is advised if using multiple samples with different base pitches and volumes.