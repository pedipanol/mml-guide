# Using PMD

Here are instructions on how to use PMD and playing back songs using driver.

## Compiling the MML into an .M file

### Original Driver

To compile MML files for PC-98 is simply a matter of running this command:

```
mc <mmlfile>
```
You can add `/p` before the filename to play the file if the setup allows it. `/c` will display each channel's length.

There are more compilation instructions that will be displayed if you run `mc` on its own. However it's best to leave these options to the [#Option header](structure.md#compile-options) instead as usually the MML itself is built around what the compilation instructions allow for.

### MML2VGM IDE

You can output a .M file in File->Export->to driver format.

### PMDDotNET

You can compile a .M file simply by dragging and dropping the MML file into `compile.bat` 

Alternatively, the compiler being a recreation of MC.EXE, it works the same way. Allowing you to add the same compile instructions when running PMDDotNETConsole.EXE in cmd.

## Playing PMD Files

Once the .M file is made they can simply be dragged and dropped into any player program listed on the [setup section](setup.md#pmd-music-file-playback) and the instructions for each are listed there.

The original driver needs more specific instructions.

### Original Driver

PMD Needs a different driver loadout depending on what the song requires/what the person's setup is. All of these files are contained within the [original driver](setup.md#original-driver-native-emulator) setup.

- PMD.COM - For songs targetting the PC-9801-26 wich has a YM2203 soundchip.
- PMDB2.COM - For songs targetting the PC-9801-76 which has a YM2608 with ADPCM functionality enabled.
- PMD86.COM - For songs targetting the PC-9801-86 which has a YM2608 plus a PCM output which will be used instead of the ADPCM.
- PMDPPZ.COM - For songs targetting the PC-9801-86 which has a YM2608 plus a PCM output, using the 8-channel PCM mode.

Once the appropriate driver is loaded, just use the following command:
```
pmp <filename>.M
```

Some songs might require additional driver loadouts, but as they're the exception, they'll be explained as it becomes relevant.