# List of MML Music Drivers

While I'm keen on eventually covering a variety of specific MML formats the same way I did for PMD, it'll take time. As most list of MML formats are kind of outdated or incomplete, I think it's worth at least making a an updated one.

If there's anything new or missing, do inform me.

## English Documented

### ppmck (NES, Famicom)
An extended version of the original mck by [BouKiCHi](https://twitter.com/boukichi_numloc), allowing for bankswitching and multiple expansion chips.

- Download: [http://ppmck.web.fc2.com/ppmck.html](http://ppmck.web.fc2.com/ppmck.html)
- English Manual: [https://woolyss.com/chipmusic/chipmusic-mml/ppmck_guide.php](https://woolyss.com/chipmusic/chipmusic-mml/ppmck_guide.php)

> To compile: put your song in the mck/songs/ folder, and run `mknsf.bat miracle` in command prompt.
>
> Edit mknsf.bat and replace ppmckc with ppmckc_e for english.

### MUCOM88 (PC-88)

A music driver made by [Yuzo Koshiro](https://twitter.com/yuzokoshiro) used in his PC-88 and Sega Genesis games. Has its own GUI which allows for instant playback.

- Download: [https://onitama.tv/mucom88/index_en.html](https://onitama.tv/mucom88/index_en.html)
- Manual: [https://github.com/onitama/mucom88/wiki/About-MUCOM88-Windows](https://github.com/onitama/mucom88/wiki/About-MUCOM88-Windows)

### ctrmml (Sega Genesis/Mega Drive)

A sound driver for the Genesis/Mega Drive by [SuperCTR](https://twitter.com/SuperCTR), has its own GUI editor that allows for instant playback.

- Download: [https://github.com/superctr/mmlgui/releases/latest](https://github.com/superctr/mmlgui/releases/latest)
- Manual: [https://github.com/superctr/ctrmml/blob/master/mml_ref.md](https://github.com/superctr/ctrmml/blob/master/mml_ref.md)


### AddMusicK (SNES)

A MML tool for hacking music into Super Mario World and making SPC files. Requires a ROM of the game.

- Download: [https://www.smwcentral.net/?p=section&a=details&id=31558](https://www.smwcentral.net/?p=section&a=details&id=31558)
- English manual included in readme.html

### mfvitools (SNES)

A MML tool by [emberling](https://twitter.com/jen_imago) for hacking music into Final Fantasy VI and making SPC files. Requires a ROM of the game.

- Download: [https://github.com/emberling/mfvitools/releases/latest](https://github.com/emberling/mfvitools/releases/latest)
- Manual: [https://github.com/emberling/mfvitools/wiki](https://github.com/emberling/mfvitools/wiki)

### Squirrel (PC-Engine)
A sound engine for the PC-Engine.

- Download: https://www.aetherbyte.com/aetherbyte-squirrel_for_pc-engine_and_turbografx-16.html
- Documentation and manual are in MANUAL.TXT.

### MXDRV (Sharp X68000)

Also known as MDX. A very popular driver for the Sharp X68000.

- Download and Manual: [https://gamesx.com/wiki/doku.php?id=x68000:mxdrv_mml_mdx_tutorial](https://gamesx.com/wiki/doku.php?id=x68000:mxdrv_mml_mdx_tutorial)

### WTD (Wonderswan)

A music driver for the Wonderswan by [Shaw02](https://twitter.com/sha_w_).

- Download: [http://shaw.la.coocan.jp/wtd/document/english/](http://shaw.la.coocan.jp/wtd/document/english/)
- Manual: [http://shaw.la.coocan.jp/wtd/document/english/tools/wtc.html](http://shaw.la.coocan.jp/wtd/document/english/tools/wtc.html)

### XPMCK (Various)

A driver based on PPMCK targetting various consoles and computers. Single chip.

- Download: [http://jiggawatt.org/muzak/xpmck/#files](http://jiggawatt.org/muzak/xpmck/#files)
- English manual included in doc\manual.html

### NRTDRV (Sharp X1)

A 2xOPM+AY-3-8910 driver for the Sharp X1.

- Download: [http://nrtdrv.sakura.ne.jp/arc/nrtdrv/?C=M;O=D](http://nrtdrv.sakura.ne.jp/arc/nrtdrv/?C=M;O=D) (needs auto https disabled)
- Manual: [http://mus.msx.click/index.php?title=NRTDRV_OFFLINE_MANUAL_EN](http://mus.msx.click/index.php?title=NRTDRV_OFFLINE_MANUAL_EN)

### mgsdrv (MSX)

A MML driver for the MSX which supports SCC and YM2413 expansions. Has a js version which allows using the browser to make or play songs made for it.

- Download (MSX DOS binaries): [https://gigamix.hatenablog.com/entry/mgsdrv/#ダウンロード](https://gigamix.hatenablog.com/entry/mgsdrv/#ダウンロード)
- Web app: [https://msxplay.com/index.html](https://msxplay.com/index.html)
- Manual: [https://mus.msx.click/index.php?title=MGSDRV_MML_11](https://mus.msx.click/index.php?title=MGSDRV_MML_11) (computer translation(?))

## Non-translated

### MML2VGM (VGM)
A MML format for a huge variety of soundchips supported by the VGM format, made by [kuma4649](https://twitter.com/kumakumakumaT_T) . Also has an IDE for instant playback, which also supports PMD, MUCOM88 and moondrv.

- Download: [https://github.com/kuma4649/mml2vgm/releases/latest](https://github.com/kuma4649/mml2vgm/releases/latest)
- Manual: [https://github.com/kuma4649/mml2vgm/blob/TAG665/mml2vgm_MMLCommandMemo.txt](https://github.com/kuma4649/mml2vgm/blob/TAG665/mml2vgm_MMLCommandMemo.txt)

### GBMC (Gameboy)

A sound driver for the Gameboy and Gameboy Color. Supports PCM playback on the Wavetable channel.

Download and Manual: [http://mydocuments.g2.xrea.com/html/gb/sounddriver.html](http://mydocuments.g2.xrea.com/html/gb/sounddriver.html)

### NSD.Lib (NES, Famicom)

A fairly popular driver for homebrew software by [Shaw02](https://twitter.com/sha_w_). There's some english explanation and some commands in the github page, but most of the documentation isn't translated.

- Download: [https://github.com/Shaw02/nsdlib/releases/latest](https://github.com/Shaw02/nsdlib/releases/latest)
- Manual: [https://shaw02.github.io/nsdlib/](https://shaw02.github.io/nsdlib/)

### Super-C (SNES)

A dedicated SNES music driver, with a focus on space saving and playback optimization.

- Download: [https://github.com/boldowa/SuperC-SPCdrv/releases](https://github.com/boldowa/SuperC-SPCdrv/releases)
- Manual: [https://github.com/boldowa/SuperC-SPCdrv/wiki](https://github.com/boldowa/SuperC-SPCdrv/wiki)

### HusiC (PC-Engine)

A driver by [BouKiCHi](https://twitter.com/boukichi_numloc) for the PC-Engine based off ppmck. While it's untranslated, a lot of the commands are shared with ppmck, so using automatic translation should get you around it if you're already familiar with the format.


- Download: [https://github.com/BouKiCHi/HuSIC/releases/latest](https://github.com/BouKiCHi/HuSIC/releases/latest)
- Manual: [https://github.com/BouKiCHi/HuSIC/blob/200904/docs/husic_tech.md](https://github.com/BouKiCHi/HuSIC/blob/200904/docs/husic_tech.md)

### moondrv (MSX)

An OPL4 driverby [BouKiCHi](https://twitter.com/boukichi_numloc) for MSX, supporting both FM and PCM. Also based on ppmck.

- Download: [https://github.com/BouKiCHi/moondrv/releases/latest](https://github.com/BouKiCHi/moondrv/releases/latest)
- Manual: [https://github.com/BouKiCHi/moondrv/blob/master/doc/moondriver.md](https://github.com/BouKiCHi/moondrv/blob/master/doc/moondriver.md)

