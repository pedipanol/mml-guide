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