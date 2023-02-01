# Setting Up PMD

PMD is popular enough that there is a bunch of different tools for using it. Here I'll list a bunch of them and how to set them up.

## Suggested Setup

If you're on Windows, I recommend trying [MML2VGM IDE](#mml2vgm-ide-windows) as it makes the writing and playback process very convenient and don't require external programs. But it's quite CPU intensive.

If it doesn't, get the following programs:

- [Original Driver](#original-driver-native-emulator)
- [ms-dos player](#ms-dos-player) or [DOSBox-X](#dosbox-x-windows-mac-linux)
- [A player of your choice](#pmd-music-file-playback)
- [YM2608 Tone Editor](#ym2608-tone-editor-windows)

For Linux and MacOS [projmd](#projmd-linux-macos) should have you covered.

## The Driver, Compilers and Editors

### Original Driver _(Native, Emulator)_

This is how to set up the original/native version of PMD. To use this method in modern machines, an [emulator](#pc-98-emulators) is needed. 

1. Download the latest version (currently 4.8s) of PMD at [KAJA's website](https://sites.google.com/site/kajapon/pmd) and extract it. Look for the text "Ver.4.8s + Source [20/01/22]".
2. Download the [English version of the compiler](https://drive.google.com/drive/folders/1fSH39Vr97_29tvjni6H7WXJlxH_HmZx0) (by Mana, Pigu and VasteelXolotl) and replace the existing MC.EXE in the PMD folder. This step isn't mandatory, but without it, most error messages will be in Japanese, which might also be corrupted into [mojibake](https://en.wikipedia.org/wiki/Mojibake) depending on your DOS emulator and its settings.
3. Transfer the folder to the PC-98 or your DOS emulator's folder.
4. For a test run, run the following commands[^1]:
```
mc /p sample.mml
```
If you get `Compile Completed`, it should have worked fine. If you encounter a problem with the first command, the emulator is not set up right. If on a real machine, try runing `pmd` instead.

### PMDDotNET _(Windows)_
_apparently works with wine except for problems with japanese fonts_

PMDDotNET is a Windows port of PMD's source code made by [kuma4649](https://github.com/kuma4649). The usage is mostly the same as the DOS version, except you can drag'n'drop both the MML and compiled files for compilation and playback, respectively.

1. Download the latest release at [Github](https://github.com/kuma4649/PMDDotNET/releases) and extract it.
2. Download [the OPNA Rhythm Sound Source (RSS) samples](http://snesmusic.org/hoot/drum_samples.zip) and extract to `(extracted folder)\player\`.
3. On the extracted folder, drag and drop the MML file onto `compile.bat`.
	(use the ones provided with [PMD](https://sites.google.com/site/kajapon/pmd) if you don't have one around)
4. Drag and drop the compiled `.M` file onto `play.bat`.

With this program You can automate the compilation process with [PMD File Watcher](#pmd-file-watcher-windows).

If the program doesn't run (and it's not a problem with the MML), it might be a Windows problem which can be addressed by running `removeZoneIdent.bat` and running it again.

### mml2vgm IDE _(Windows)_
_apparently works with wine except for problems with japanese fonts_

An open source MML editor for PMD, MUCOM88, M98, moondriver, and its own mml2vgm format made by [kuma4649](https://github.com/kuma4649). It includes the compiler and player in the program itself, so playback can be done at the press of a button, including highlights on the currently playing notes.

1. Download [mml2vgm](https://github.com/kuma4649/mml2vgm/releases/tag/TAG653) and extract it.
2. Download [the OPNA Rhythm Sound Source (RSS) samples](http://snesmusic.org/hoot/drum_samples.zip) to `(extracted folder)\mml2vgmIDE\`.
3. In the same folder, run `mml2vgmIDE.exe`.
4. You can create a template PMD MML file by clicking File -> New -> New .mml
5. Press F5 for playback, or Ctrl+F5 for highlighted playback. Other shortcuts are displayed at the bottom of the screen.

If the program doesn't run properly, it might be a Windows problem which can be addressed by running `removeZoneIdent.bat` in the parent folder and running it again.

### projmd (_Linux, MacOS_)

A Linux wrapper for PMD and DOS-Box by opna2608, needs [Nix](https://nixos.org/manual/nix/stable/installation/installing-binary.html).

1. Download or Clone the [projmd repository](https://github.com/OPNA2608/projmd)
2. Download the [original PMD files](#original-driver-native-emulator) and extract them into the /PMD/ folder.
3. Download [the OPNA Rhythm Sound Source (RSS) samples](http://snesmusic.org/hoot/drum_samples.zip) and extract it into the /pmdmini/ folder.
4. Run shell.nix, this should add a new `pmd` command.
5. The `pmd` command has the following functions:
	- `pmd compile` compiles the MML file
	- `pmd play` renders and plays the file
	- `pmd watch` watches the MML files, compiling and playing whenever it's updated
	- `pmd shell` puts you in a DOSBox session with the PMD tools in C:\ and the workspace directory in D:\


### PMD File Watcher _(Windows)_
An open source tool by Rerrahkr for automating the process of compiling and playing songs. It works by setting it to watch whenever the MML file is updated, which triggers it to compile and then play the resulting file.

For using it you need to either have  [PMDDotNET](#pmddotnet-windows) or the [MS-DOS Player](#ms-dos-player) setup beforehand.

1. Download [PMD File Watcher](https://github.com/rerrahkr/PMDFileWatcher/releases/latest) and extract it.
2. In File -> Settings, in MML Compiler, navigate and select `PMDDotNETConsole.exe` (if using PMDDotNET) or `MC.EXE` (if using PMD), then change the other options if you desire.
	
	2.1. If using the original `MC.EXE`, then also select `msdos.exe` after checking "MS-DOS Player", just below the MML Compiler settings.

3. If you want the song to play after compiling, go to the Play tab, select the player, and check Autoplay.

## PMD Music File Playback

PMD compiles the MML scripts into a binary form with the extension `.M`  by default. This can be changed as desired with the `#Filename` header. Commonly, people use the `.M2` for specifically OPNA tracks and `.MZ` for tracks using PPZ, so these are the 3 formats that will be recognized by most players.

### DOS _(Native, Windows, Mac, Linux)_

Same as the compiler, an [emulator](#pc-98-emulators) is needed for running this method in modern machines. Steps 1 and 2 are the same as the one you set up in PMD.

1. Download the latest version (currently 4.8s) of PMD at [KAJA's website](https://sites.google.com/site/kajapon/pmd) and extract it. Look for the text "Ver.4.8s + Source [20/01/22]".
2. Transfer the folder to the PC-98 or your DOS emulator's folder.
3. On DOS, run the desired driver (next section details them).
4. Run `pmp <filename>` for playback¹.

[MSDP](https://www.vector.co.jp/soft/dos/art/se056754.html) and [FMDSP](https://www.vector.co.jp/soft/dos/art/se022227.html) are 2 alternate players that include an interface for loading songs more easily, and a song visualizer which includes a piano view and shows other details. To use them, extract them to the folder the driver is in, run the driver, then load MDSP or FMDSP.

### FMPMD2000 _(Windows)_
A source-available plugin-based player for Windows developed by C60. It was the first Windows player that included a sound emulator for playback. Because of that, it has a few quirks which don't make it a 100% accurate player, but it's definitely very convenient.

1. Download FMPMD2000(English Version) and PMDWin.dll at [C60's website](http://c60.la.coocan.jp/download.html).
2. Download [the OPNA Rhythm Sound Source (RSS) samples](http://snesmusic.org/hoot/drum_samples.zip).
3. Extract them to the same folder.
4. Run `FMDPMDE.exe`. Drag'n'drop PMD files into it to play the songs.

Additional configurations:
In the main window, check **SSG+RHY**, as it's needed for most songs (mainly Touhou). You will want to turn it off and turn on **PPS** when playing songs that use PPS for PCM, but they are few.

The Window bar shows additional info during playback. The most useful are **Volume** and **PartMask** which will allow you to change the mixing on each part and mute individual channels, respectively. **Parameter** will show some information about the state of each channel as the song plays.

In **File -> Environment Setting**, you can change things such as the sound device, and the default directory for PCM files and RSS samples (in case you didn't extract them to the same folder).

By default, looped songs play forever. To change this, in **File -> Environment Setting -> Sound (2)**, check "**Auto fadeout and stop for each music**", and then set how many loops and the fadeout length you desire.

(Optional) On `FMPMD.ini`, change `Frequency=` to 55467 and `FMCalc55k=` to 0. This will reduce the CPU load and prevent slowdowns and crashes on weaker PCs. After doing this, do not change anything in Envoronment Setting, since it'll reset the frequency to whatever is selected.

### inFMPMD and foo_input_fmpmd *(Windows)*
These are plugins for Winamp and foobar2000, respectively, that use FMPMD's plugins for PMD playback. While this makes the configuration process more annoying than FMPMD's default player, the convenience of using a familiar player makes it worth it. You may skip this first part if you already configured FMPMD2000.

1. Download PMDWin.dll at [C60's website](http://c60.la.coocan.jp/download.html).
2. Download [the OPNA Rhythm Sound Source (RSS) samples](http://snesmusic.org/hoot/drum_samples.zip).
3. Extract them to the same folder.

### Winamp
4. Download [inFMPMD](http://www.purose.net/befis/download/).
5. Extract it to `(Winamp's installation folder)\Plugins\`.
6. Copy `PMDWin.dll` and the 2608 Rhythm samples, and paste both into Winamp's plugin folder.
7. On Winamp, go to Options -> Preferences -> Plugins -> Input, click inFMPMD v1.4.0, then on Settings below. On the Mixer tab, disable "Enable PPS" and turn on "RHYTHM with PPS" (must be reversed for some songs, but there are more tracks that need this than vice versa).

### foobar2000
8. Download [foo_input_fmpmd](https://ux.getuploader.com/foobar2000/download/31).
9. Extract it to `(foobar's installation folder)\components\`.
10. Open foobar2000, then go to File -> Preferences -> Tools -> FMP/PMD Input Settings.
11. On Driver Modules Location, point it to `PMDWin.dll`'s folder, and the RSS Samples'.  
12. On PMDWinDriver settings, uncheck "Use PPS" and check "OPNA Rhythm with SSG Effect" (must be reversed for some songs, but there are more tracks that need this than vice versa).

### 98fmplayer _(Windows)_
Open source player for Windows developed by myon98. It replicates the look of FMDSP on the PC-98 and includes a live osciloscope viewer.

1. Download the [latest release](https://github.com/myon98/98fmplayer/releases).
2. For RSS playback, you need `ym2608_adpcm_rom.bin` on the same folder (you'll need to find it for yourself, sorry).
3. Drag'n'drop PMD files into it to play them.
4. (Optional) Add PC-98's `FONT.ROM` (again, look for it yourself) to use PC-98's font instead of MS Gothic.

### MDPlayer _(Windows)_
An open source multi-format chiptune player by [kuma4649](https://github.com/kuma4649). It uses PMDDotNet for playback. It's a bit heavy, so it might not perform well on weaker machines.

1. Download the [latest release](https://github.com/kuma4649/MDPlayer/releases/latest).
2. Drag'n'drop PMD files into it to play.

Additional options can be changed on the options menu. It can be reached by clicking the wrench icon.

You can turn on a visualizer by clicking the icon that looks like a sound spectrum (says Keyboard when the cursor hovers over it), then Primary -> OPN -> OPNA.

## Making/Converting FM Instruments and Banks
Editting FM instruments in the MML itself can be rough, as compilation and playback are required to hear the changes. KAJA addressed this by making an FM instrument editor.

### DOS _(Native, Windows, Mac, Linux)_
Included in the PMD 4.8s package, PMD Voice Editor is a program for editting, loading and saving PMD's instrument patches and banks. Follow these steps:

1. Download "PMD98用 Preset FM" from [KAJA's Website](https://sites.google.com/site/kajapon/pmd) and put it in [PMD's folder](#original-driver-native-emulator)
2. In DOS, run `VEDSE.EXE EFFEC.FF` in PMD's folder.[^1]
3. Press any of the keys in the Z row of the keyboard, this should play a sound.[^2]
4. Quitting with Esc will overwrite and save the loaded file.

The .FF files it outputs are FM instrument banks which can be loaded in the MML via the `#FFFile` header.

Operation of the program itself is explained in VEDSE.DOC. It's in Japanese, but machine translation should suffice in making things understandable. Important to note is that the SSG-EG parameters have to be input in the script as a command.

### YM2608 Tone Editor _(Windows)_
Developed by Rerrahkr, YM2608 Tone Editor is an OPN instrument editor for Windows. It can read many instrument and bank formats, as well as PMD's song files, which is convenient for getting instruments.

1. Download [YM2608 Tone Editor](https://github.com/rerrahkr/YM2608-Tone-Editor/releases/latest) and extract it.
2. In the main window, the keyboard will let you test the instrument.
3. Instruments can be loaded with File -> Open Tone **or** -> Open Bank.
4. You can output the currently selected instrument to MML with File -> Convert to text, and then selecting PMD.
5. You can also output an .FF file in File -> Save Bank As..., and then selecting PMD Bank (.ff).

It can be convenient to have it open as you type the MML to adjust or load instruments as you need, and test them without having to compile the song. Important to note is that the SSG-EG parameters have to be input in the script as a command.

## PC-98 Emulators

### NekoProject II fmgen (Windows)

This is the best option for audio because while it's not well rounded it allows you to use custom sound emulations of which 2 are essential:

1. Download np2fmgen.7z [here](http://nenecchi.kirara.st/) and extract it
	
>This website is http only. If it gives a japanese error message, it`s probably because your browser is in https-only mode.

2. Download fmdllset.7z from the same page and extract fmgen.dll and genpfm.dll to the emulator folder.
3. Launch your prefered instance of the emulator and then close it.
4. Open the created .ini file in a text editor, then add the following line:
	
	```
	FMGenDll=fmgen.dll
	```
	or
	```
	FMGenDll=genpfm.dll
	```

>fmgen.dll provides the most well rounded emulation so it's prefered, unless you want to use SSG-EG, which genpfm.dll has support for.

To use this emulator you'll need a hard disk image. [YAHDI](https://nfggames.com/forum2/index.php?topic=5463.0) is the most used one and I recommend it. Download it and follow the guide to convert it to `.nhd`.

After setting up, follow the [Original Driver](#original-driver-native-emulator) setup for PMD.

### DOSBox-X (Windows, Mac, Linux)

While not as compatible as NekoProject II, DOSBox-X can be a good choice for using PMD as it use a local folder as an HD instead of a hard disk image, making file management much easier.

To use it:

1. Download [DOSBox-X](github.com/joncampbell123/dosbox-x/releases/latest/) and extract it.

2. In the program's folder, open dosbox-x.conf in a text editor
3. Search for `machine` until you find the setting that is set to `= svga_s3` (it's in line 215 as of writing this) then change it to `= pc98`

This should already give you a working setup, of which the folder "drivez" is used for file management. There's some [additional configurations](https://dosbox-x.com/wiki/Guide%3APC‐98-emulation-in-DOSBox‐X) you might want to make but they aren't necessary for the purposes of this guide.

After setting up, follow the [Original Driver](#original-driver-native-emulator) setup for PMD.

### MS-DOS Player

This is an emulator for running general simple DOS programs in command line. The only reason you'd want this is if you only want to run the original `MC.EXE` and no other PC-98 program.

1. Download [MS-DOS Player](http://takeda-toshiya.my.coocan.jp/msdos/index.html) and open the file in
2. Extract the msdos.exe file contained in either "msdos\binary\i486_x86" or "..\i486_x64" according to your operating system to the folder you'll work in.
3. Following the [Original Driver](#original-driver-native-emulator) guide put just the translated MC.EXE in the folder.

You can automate the compilation process with [PMD File Watcher](#pmd-file-watcher-windows).

---

[^1]: : To navigate to different folders in DOS, use `cd <folder>`. To go outside of a folder, use `cd ..`. Folder paths always use `\` as a separator. If using a PC-98 or a DOS emulator in PC-98 mode, and also depending on the font, a `\` might appear as a `¥`. This is normal.

[^2]: Different keyboards, alongside IME settings, might make this not function correctly. If so, try changing the IME settings, or using a different keyboard if available.