# SSG-EG

SSG-EG is a feature of the OPN that replaces the ADSR envelope with a wave shape. It works similar to an amplitude LFO, except it can get to frequencies high enough to be perceived as a note pitch, and modify the base sine wave. This allows for creating more complex sounds with the FM channels.

However it`s a pretty complex feature that not even many musicians used before the 2000's. Because of this not many players and emulators support it, it wasn't even implemented into PMD properly until KAJA updated it in 2020.

## Players

First you must ensure the player you're using supports it.

### FMPMD2000 (Windows)

If you followed this guide, it should already support SSG-EG. If you had downloaded it before 2020, you should update it.

### 98fmplayer (Windows)

It doesn't support SSG-EG at all.

### Emulator

### np2fmgen

However it's a complicated feature that you have to build your instrument speficically to use, or else it won't work. Using an instrument editor such as YM2608 tone editor on Windows or VEDSE on DOS will allow you to test the values properly.

( `SE<operator>,<value>` )

After creating a instrument and putting it in the script, call the instrument with `@` and then use the `SE` command, assigning the operators then the shape in the second value.

```
@0 0 0 31 19 31 15 2 29 0 7 7 0 31 11 31 15 1 28 0 0 3 0 24 11  0 15 2 29 0 2 0 0 27 13 12 15 2  2 0 2 0 0

A	@0 SE3,8 l8 o3 [c4gc2.<g16g16>[c<g]3]4  ;
```

It's important to note that SSG-EG as a feature was barely used until the 2000's, and support for SSG-EG was only added when KAJA updated PMD in 2020. Because of this, many players and emulators never properly implemented it at all.

If you try playing the script above and it doesn't sound like a timpani instrument, it should be a bad emulation problem:
 - If using FMPMD, update it to the latest version
 - In NekoProject, only np2fmgen using the genpfm.dll supports it
 - PMDDotNet/MML2VGM doesn't support it

If you're gonna use SSG-EG unfortunately you'll need to use the few players that support it.
