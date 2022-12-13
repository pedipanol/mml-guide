# ADPCM (PPC)

The YM2608 soundchip has a 4-bit ADPCM channel, which has 256kB of RAM available for storing samples. This is the main limitation to have in mind when creating sample banks for ADPCM, as the PPC file itself cannot go above 256kB as well.

The lack of this RAM is the reason the 86 soundboards don't natively support this channel. Thankfully, it can be modded with with the Chibi-oto add on, which adds the missing RAM and gives full functionality to the channel.

## Making a PPC File

_(Make sure to do everything in the main [PCM](6PCM.md\#handling-pcm) page before proceeding with this guide)_

### Preparing the Samples

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

### Sample Conversion

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

### Packing it up in a PPC Bank

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


## Sequence and Commands

In the MML, to load a PPC file when playing the song, add the following header:

```
#PPCFile    <filename>.PPC
```

The PPC file needs to always be in the same folder as the .MML or .M file for the song to be played properly. Some players won't play the song at all if the intended sample bank is not present or pre-loaded in memory.

To sequence the ADPCM, use the `J` channel.

### Set instrument (`@<value>`)

To choose the PCM isntrument, use `@`, in which the value corresponds to the sample ID in the PPC. This is will make the sample play as-is once when a note is played.

### Set looped instrument (`@<value>,<start>,<end>,<release>`)

By adding values after setting the instrument, PMD can loop the samples from specific loop points.

However, ADPCM's case is a complicated process because of its encoding format.

Firstly, the step count doesn't correspond to the same as the original sample. OPNA loads ADPCM samples in 32-bit chunks, so the count for this command is every 32-bits, or every 8 samples. If a sample is planned to loop, ensure that the loop points are multiples of 8, and divide accordingly. Also, `<end>` refers to the last chunk played for the loop, so be sure to not accidentally specify the chunk *after* the loop!

Secondly, ADPCM doesn't natively support loop points, instead looping the entire sample. PMD circumvents this by changing the sample start offset just after beginning sample playback, but due to how the sample data is interpreted, this changes the actual waveform output, making it dificult to make a perfectly looping sample and sometimes lowering the volume. The technical reason is because the conversion from 4-bit ADPCM to an audio signal for a given sample depends on the previous output.

### Set volume envelope

You can set a volume envelope with the `E` command and it works as [specified before in the guide](4Chipcom.md\#volume-envelopes).

### Notes

As mentioned before, if the sample is encoded as 16kHz, `o5g` will play the same as the source file.

If you're using a pitched sample, and its pitch is any note other than a G, it's recommended to use a global transposition `_M<value>` and/or global detune command `DD<value>` to pitch it accordingly.

Making a macro is advised if using multiple samples with different base pitches and volumes.
