# Professional Music Driver (PMD)

PMD is a driver used for making music for the PC-98, PC-88, X68000, FM Towns and IBM PCs. Being a consumer-grade driver allowed it to be used for many games, becoming one of the most popular drivers at the time, along with FMP. Notable examples of games using it are Touhou Project and Grounseed.

Developed by [Masahiro Kajihara](https://twitter.com/kajaponn), from 1989 PMD was developed and improved as he made his carreer programming and writing music for games, including its ports to different platforms. Development stopped around the year 2000 with the transition to modern computers, but in 2019 for the celebration of its 30th anniversary KAJA not only updated it with support for new commands as well as released its [source code](https://sites.google.com/site/kajapon/pmd) to the public.

WIth its latter updates being mostly focused on PC-98 music, there are features explained in this guide that the other versions might not support, so this guide is focused on that platform.

_I'm planning on making an addendum explaining usage and differences for PMDIBM which I've used before, maybe others will come in the future as well._

## Setting Up PMD

There are 3 methods for using PMD depending on your computer and OS.

### DOS _(Native, Windows, Mac, Linux_)
For using this method in modern machines an emulator is needed. I recommend [DOSBox-X](https://dosbox-x.com) set up to PC-98 mode because it doesn't require using external programs for managing files, but the audio is not the best. You can skip configuring it to PC-98 mode if you want to use a windows-based player (next section).

1. Download the latest version (currently 4.8s) of PMD in [KAJA's website](https://sites.google.com/site/kajapon/pmd) and extract it.
2. Download the [english version of the compiler](https://drive.google.com/drive/folders/1fSH39Vr97_29tvjni6H7WXJlxH_HmZx0) (by Mana, Pigu and VasteelXolotl) and replace the existing MC.EXE in the PMD folder.
3. Transfer the folder to the PC-98 or your DOS emulator's folder.
4. For a test run, run the following commands:
```
pmdb2
mc /p sample1.mml
```
If you get `Compile Completed` and the song starts playing it should have worked fine. If you a problem with the first command the emulator is not set up right. If on a real machine try runing `pmd` instead.

### PMDDotNET _(Windows)_
_let me know if this works on wine if anyone tries it_

PMDDotNET is a windows port of PMD's source code made by [kuma4649](https://github.com/kuma4649). The usage is mostly the same as the DOS version except you can drag'n drop both the MML and compiled files for compilation and playback respectively.

1. Download the latest release on [Github](https://github.com/kuma4649/PMDDotNET/releases) and extract it.
2. Drag and drop the MML file into compile.bat .
	(use the ones in the [pmd ](https://sites.google.com/site/kajapon/pmd) if you don't have one around)
3. Drag and drop the compiled .M file in play.bat .

If the program doesn't run (and it's not a problem with the MML), it might be a windows problem which can be addressed by running "removeZoneIdent.bat" and running it again.

### mml2vgm IDE _(Windows)_
_let me know if this works on wine if anyone tries it_

An open source MML editor for PMD, MUCOM88, M98, moondriver and its own mml2vgm format made by [kuma4649](https://github.com/kuma4649). It includes the compiler and player on the program itself, so playback can be done at the press of a button, including highlights ont he currently playing notes.

1. Download [MML2VGM](https://github.com/kuma4649/mml2vgm/releases/tag/TAG653) and extract it.
2. In (extracted folder)\mml2vgmIDE\ run mml2vgmIDE.exe
3. You can create a template PMD MML file by clicking File -> New -> New .mml
4. Press F5 for playback, Ctrl+F5 for highlighted playback. Other Shortcuts are displayed at the bottom of the screen.

If the program doesn't run properly, it might be a windows problem which can be addressed by running "removeZoneIdent.bat" in the parent folder and running it again.

### PMD File Watcher _(Windows)_
An open source tool by Rerrahkr for automating the process of compiling and playing songs. It works by setting it to watch whenever the MML file is updated, which triggers it to compile and then play the resulting file.

1. Download [PMD FIle Watcher](https://github.com/rerrahkr/PMDFileWatcher/releases/latest) and extract it.
2. In File -> Configuration in MML Compiler navigate and select PMDDotNETConsole.exe or MC.EXE then change the other options if you desire.
(if using MC.EXE, you'll need to use [MS-DOS Player](http://takeda-toshiya.my.coocan.jp/msdos/index.html) and configure it below).
3. If you want the song to play after compiling, go to the Play tab, select the player and check Autoplay.


## PMD Music File Playback
PMD compiles the MML scripts into binary form with the extension `.M`  by default. This can be changed as desired with the `#Filename` header. Commonly, people use the `.M2` for specifically OPNA tracks and `.MZ` for tracks using PPZ, so theses are the 3 formats that will be recognized by most players.

### DOS _(Native, Windows, Mac, Linux)_
Same as the compiler, an emulator is needed for running this method in modern machines. This is the only way to play the non-pc98 formats supported by PMD. Steps 1 and 2 are the same as the one you set up in PMD.

1. Download the latest version (currently 4.8s) of PMD in [KAJA's website](https://sites.google.com/site/kajapon/pmd) and extract it.
2. Transfer the folder to the PC-98 or your DOS emulator's folder.
3. On DOS, run the desired driver (next section details them)
4. Run `pmp <filename>` for playback.

[MSDP](https://www.vector.co.jp/soft/dos/art/se056754.html) and [FMDSP](https://www.vector.co.jp/soft/dos/art/se022227.html) are 2 alternate players that include a interface for loading songs more easily and a song visualizer which includes a piano view and show other details. To use them, extract them to the folder the driver is in, run the driver then load MDSP or FMDSP.

### FMPMD2000 _(Windows)_
A source-available plugin-based player for WIndows developed by C60. It was the first windows player that included a sound emulator for playback. Because of that it has a few quirks which don't make it a 100% accurate player, but it's definitely very convenient.

1. Download [FMPMDE031.lzh and PMDWin.dll](http://c60.la.coocan.jp/download.html) in C60's website.
2. Download [the OPNA Rhythm Sound Source (RSS) samples](http://snesmusic.org/hoot/drum_samples.zip).
3. Extract them in the same folder.
4. Run FMDPMDE.exe. Drag'n drop PMD files into it to play the songs.

Additional configurations:
In the main window, check **SSG+RHY** as it's needed for most songs (mainly touhou). You will want to turn it off and turn on **PPS** when playing songs that use PPS for PCM but they are few.

The Window bar shows additional info during playback. The most useful are **Volume** and **PartMask** which will allow you to change the mixing on each part and mute individual channels, respectively. **Parameter** will show some information about te state of each channel as the song plays.

In **File -> Environment Setting** you can change things such as the sound device and change the default directory for PCM files and for the RSS samples (in case you didn't extract them to the same folder).

By default looped songs play forever. To change this, in **File -> Environment Setting -> Sound (2)**, check "**Auto fadeout and stop for each music**", and then set how many loops and the fadeout length you desire.

(Optional) On FMPMD.ini, change "Frequency=" to 55467 and "FMCalc55k=" to 0. This will reduce the CPU load and prevent slowdowns and crashes on weaker PCs. After doing this, do not change anything in Envoronment Setting, since it'll reset the frequency to whatever it's selected.

### inFMPMD and foo_input_fmpmd *(Windows)*
These are plugins for Winamp and foobar2000, respectively, that use FMPMD's plugins for PMD playback. While this makes the configuration process more annoying than FMPMD's default player, the convenience of using a familiar player makes it worth it. You may skip this first part if you already configured FMPMD2000.

1. Download [PMDWin.dll](http://c60.la.coocan.jp/download.html) in C60's website.
2. Download [the OPNA Rhythm Sound Source (RSS) samples](http://snesmusic.org/hoot/drum_samples.zip).
3. Extract them in the same folder.

##### Winamp
4. Download [inFMPMD](http://www.purose.net/befis/download/)
5. Extract it to (Winamp's installation folder)\Plugins\ .
6. Copy PMDWin.dll and the 2608 Rhythm samples and paste both on the Winamp's plugin folder.
7. On Winamp, go to Options->Preferences->Plugins->Input, click inFMPMD v1.4.0, then on Settings below. On the Mixer tab disable "Enable PPS" and turn on "RHYTHM with PPS" (must be reversed for some songs, but there are more tracks that need this than vice versa).

###### foobar2000
4. Download [foo_input_fmpmd](https://ux.getuploader.com/foobar2000/download/31)
5. Extract it to (foobar's installation folder)\components\.
6. Open foobar2000 then go to File -> Preferences -> Tools -> FMP/PMD Input Settings  
7. On Driver Modules Location, point it to PMDWin.dll's folder and the RRS Samples'.  
8. On PMDWinDriver settings uncheck "Use PPS" and check "OPNA Rhythm with SSG Effect" (must be reversed for some songs, but there are more tracks that need this than vice versa).

### 98fmplayer _(Windows)_
Open source player for windows developed by myon98. It replicates the look of FMDSP on the PC-98 and includes a live osciloscope viewer.

1. Download the [latest release](https://github.com/myon98/98fmplayer/releases).
2. For RSS playback, you need "ym2608_adpcm_rom.bin" on the same folder (google it).
3. Drag'n drop PMD files into it to play.
4. (Optional) Add PC-98's "FONT.ROM" (google it) to use PC-98's font instead of MS Gothic.

### MDPlayer _(Windows)_
An open source multi-format chiptune player by [kuma4649](https://github.com/kuma4649). It uses PMDDotNet for playback. It's a bit heavy so it might not perform well in weaker machines.

1. Download the [latest release](https://github.com/kuma4649/MDPlayer/releases/latest)
2. Drag'n drop PMD files into it to play.

Additional options can be changed on the options menu that can be reached by clicking the wrench icon.

You can turn on a visualizer by clicking the sound spectrum-like icon (says Keyboard when you hover it) -> Primary -> OPN -> OPNA.

## Making/Converting FM Instruments and Banks
Editting FM instruments in the MML itself can be rough as it takes the compilation and playback to be able to hear the changes. KAJA addressed this by making an FM instrument editor

### DOS _(Native, Windows, Mac, Linux)_
Included in the PMD 4.8s package, PMD Voice Editor is a program for editting, loading and saving pmd's instrument patches and banks. Follow the steps 

1. Download "PMD98用 Preset FM" from [KAJA's Website](https://sites.google.com/site/kajapon/pmd) and put it in PMD's folder (read the above DOS guides if you don't have it set up).
2. On DOS, run `VEDSE.EXE EFFEC.FF` in PMD's folder.
3. Press any of the keys in the Z row of the keyboard, this should play a sound.
4. Quitting with Esc will overwrite the loaded file.

The .FF files it outputs are FM instrument banks which can be loaded in the MML via the \#FFFile header.

Operation of the program itself is explained in VEDSE.DOC. It's in japanese but a google translation should suffice in making things understandable. Important to note that the SSG-EG parameters have to be input in the script as a command.

### YM2608 Tone Editor _(Windows)_
Developed by Rerrahkr, YM2608 Tone Editor is an OPN instrument editor for Windows. It can read many instrument and bank formats as well as PMD's song files, which is convenient for getting instruments.

1. Download [YM2608 Tone Editor](https://github.com/rerrahkr/YM2608-Tone-Editor/releases/latest) and extract it.
2. The keyboard will let you test the instrument.
3. Instruments can be loaded in FIle->Open Tone or Open Bank.
4. You can output the current selected instrument to MML in File->Convert to text and then selecting PMD.
5. You can also output an .FF file in File -> Save Bank As... and slecting PMD Bank (.ff)

It can be convenient to have it open as you type the MML to adjust or load instruments as you need and test them without having to compile the song. Important to note that the SSG-EG parameters have to be input in the script as a command.