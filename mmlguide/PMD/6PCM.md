# Handling PCM

PMD has extensive PCM capabilities beyond just the ADPCM of the OPNA. It has a difficult setup process for getting the PCM files, but once that's done it's pretty simple to use.

## The four types of PCM

The PCM types PMD has are:

- ADPCM: using OPNA's internal ADPCM channel
- 86PCM: uses the PC98's 86 soundboard's PCM as an alternate to the ADPCM
- PPZ8: uses the PC98's 86 soundboard's PCM to generate up to 8 PCM channels.
- PPSDRV: uses SSG channel 3 to generate up to 2 PCM channels.

All of them have different capabilities, and whatever possibility of using them on real machines will depend on the soundboard setup. This is the reason many PMD soundtracks that use ADPCM will have a 86PCM version.

That being said most PC-98 audio aficionados will have a 86 soundboard with a Chibi-Oto, enabling the use of all the 4 types.

## Making a PCM conversion workspace

To make your own PCM samplebanks, you'll need to set up a workspace on DOS. For this I recommend