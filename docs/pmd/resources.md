# List of Resources for PMD

Here's a variety of other links either directly related to PMD or that will be useful in writing MML in one way or another. If any good resources you know of haven't been listed do message me about them.

## Guides, Documentation

Various writeups, guides and documentation for PMD in english by other users.

##### English Translation of PMDMML.MAN
[https://pigu-a.github.io/pmddocs/pmdmml.htm](https://pigu-a.github.io/pmddocs/pmdmml.htm)</br>
This is the original documentation of PMD MML translated to english and converted to a web page.

##### Alternate documentation of PMD's commands by HertzDevil
[https://battleofthebits.org/lyceum/View/Professional%20Music%20Driver%20Effects%20Commands](https://battleofthebits.org/lyceum/View/Professional%20Music%20Driver%20Effects%20Commands)</br>
A documentation of PMD's commands that differs from the original and keeps things objective and brief. I recommend using it as a reference manual.

##### Overview of PMD at BattleoftheBits
[https://battleofthebits.org/lyceum/View/Professional+Music+Driver+%28PMD%29](https://battleofthebits.org/lyceum/View/Professional+Music+Driver+%28PMD%29)</br>
An quick overview of PMD and a setup tutorial, also made by me (with community contributions).

##### NoyemiK's guide to PMD MML (archive)
[https://web.archive.org/web/20211121010954/https://delmunsoft.com/PMD%20Programming.html](https://web.archive.org/web/20211121010954/https://delmunsoft.com/PMD%20Programming.html)</br>
A tutorial of PMD for beginners that goes into the process at a step by step manner. Unfortunatelly it has been taken down but an archive has been kept and NoyemiK has allowed me to link it.

##### Mana Kazami's PMD MML overview
[https://dl.dropboxusercontent.com/s/xh1yi83vdab7lnj/PMDMML.txt](https://dl.dropboxusercontent.com/s/xh1yi83vdab7lnj/PMDMML.txt)</br>
A brief overview of PMD's MML basic structure and commands. This doesn't cover everything but it was the guide I used when I first learned PMD MML.

##### mrnuage's video guide for PMD
 Part 1: [https://www.youtube.com/watch?v=xEiCngF5v1A](https://www.youtube.com/watch?v=xEiCngF5v1A)</br>
 Part 2: [https://www.youtube.com/watch?v=FyKxZXTvBvg](https://www.youtube.com/watch?v=FyKxZXTvBvg)</br>
A video guide for PMD. Part 1 covers the setup part and Part 2 has them sequencing a song as they explain the commands.

---

## Song Archives

Archives with various songs in PMD .M format for playback.

##### MDXO FMPMD Archive
[https://nfggames.com/PC98/index.php/Music/](https://nfggames.com/PC98/index.php/Music/)</br>
An archive containing a lot of old song files for PMD and FMP. Contains both original songs/game soundtracks as well as arrangements of game songs and other types of media. FMP (.OVI) Files can be played back with [FMPMD2000](setup.md#fmpmd2000-windows) (download WinFMP.dll) and [98fmplayer](setup.md#98fmplayer-windows).

##### Hoot Archive
[http://hoot.joshw.info/pc98/](http://hoot.joshw.info/pc98/)</br>
An archive of PC98 soundtracks that can be played back with [hoot](https://battleofthebits.org/lyceum/View/hoot). Only a few of them use PMD.

---

## Where to find MML scripts

Most (western) PMD MML musicians share their MML scripts when uploading songs that use it. So a search for a song using PMD MML on youtube or soundcloud most likely will lead so songs that will have the source script linked in the description.

Unfortunately there are no single links for an archive of sorts. But I should put this information here.

## FM Instruments

FM instrument files ready for use with PMD, in both MML instrument definition and .FF file.

In addition to these, you can convert most 4-op instrument formats (including other soundchip's), extract instruments from songs and make your own with [YM2608 Tone Editor](setup.md#ym2608-tone-editor-windows).

##### PMD98's preset instruments
[https://sites.google.com/site/kajapon/cabinet/PMD98_PresetVoices.zip?attredirects=0](https://sites.google.com/site/kajapon/cabinet/PMD98_PresetVoices.zip?attredirects=0)</br>
Includes both .FF files that can be opened with [YM2608 Tone Editor](setup.md#ym2608-tone-editor-windows) for testing or modifying, and plain text instrument definitions for pasting directly into the MML.

##### brickblock369's FM instruments

[https://github.com/pedipanol/mml-guide/tree/main/docs/pmd/files](https://github.com/pedipanol/mml-guide/tree/main/docs/pmd/files) </br>
The mml file contains the instrument data for copy and pasting into the MML script. The FF file can be used with #FFFile and opened with [YM2608 Tone Editor](setup.md#ym2608-tone-editor-windows) for testing or modifying instruments.

##### valsound's FM sound library

[https://web.archive.org/web/20200130230847/http://valsound.fc2web.com/fm_lib.html](https://web.archive.org/web/20200130230847/http://valsound.fc2web.com/fm_lib.html)</br>
Click the yellow square to get to each category's page. Instruments are in plain text and only need a `@<value>` defined before the values to work with PMD.

##### Hertzdevil's Touhou Instrument archive

[https://pastebin.com/YbPCdeR7](https://pastebin.com/YbPCdeR7)</br>
**Needs #DT2Flag on**. ZUN's touhou instruments are mostly a variation of the PMD98 presets. Hertzdevil compiled not only all the instruments but also all the slight variations he used for each track.

---

## PCM Sample Banks

PCM Files of all kinds ready for use with PMD.

##### MDXO FMPMD Archive
[https://nfggames.com/PC98/index.php/Music/](https://nfggames.com/PC98/index.php/Music/)</br>
Includes a variety of sample banks along with the song files.

---

## Convert from PMD

Some tools for converting .M files into various other formats

##### PMD2S98

[http://www.vesta.dti.ne.jp/~tsato/soft_s98v3.html](http://www.vesta.dti.ne.jp/~tsato/soft_s98v3.html)</br>
Converts PMD's .M files into .S98 files, which is similar to .VGM. Converter is very limited and doesn't support some features such as ADPCM.

##### pmd2rcp

[https://sites.google.com/site/kajapon/pmd](https://sites.google.com/site/kajapon/pmd)</br>
Included with MMD, a MIDI driver for the PC98 also made by KAJA. You can use pmd2rcp to convert PMD files to a recomposer (.RCP) file, which then can be converted to .MID with a tool such as [https://www.vector.co.jp/soft/win95/art/se182775.html](RCP2MID).

##### pmd2mid

[https://github.com/ValleyBell/MidiConverters](https://github.com/ValleyBell/MidiConverters)</br>
Included with ValleyBell's Midi Converters, unfortunately it's source code only so needs to be built.

##### PMDRC

[https://judstyle.jp/wiki/PMDRC](https://judstyle.jp/wiki/PMDRC)</br>
A .M file decompiler for extracting MML data from songs. It's very limited and not every song is completely compatible, but it might help.

---

## Convert to PMD MML

Some tools for converting various formats into PMD MML scripts.

These can be helpful but as they can't give a complete conversion you still need to edit the files yourself.

##### convfmml

[https://github.com/rerrahkr/ConvFMML](https://github.com/rerrahkr/ConvFMML)</br>
A .MID -> MML converter for various formats including PMD. Only converts sequence data and very few commands.

##### s3mml

[https://github.com/jangler/s3mml](https://github.com/jangler/s3mml)</br>
A python .S3M (OPL2 tracker module) -> MML converter for PMD.</br>
It converts both sequence and instrument data (an approximation of the OPL instruments is recreated with the OPNA's format), but very few commands are supported.

There's also a fork for [PMDIBM](https://github.com/jangler/s3mml/tree/opl2)

##### dmf2pmd

[https://battleofthebits.org/lyceum/View/dmf2pmd.py](https://battleofthebits.org/lyceum/View/dmf2pmd.py)</br>
A python Sega Genesis .DMF (Deflemask Module) -> MML converter for PMD.</br>
Convers both sequence and instrument data (except for SSG-EG) are converted. Read the documentation for supported commands.