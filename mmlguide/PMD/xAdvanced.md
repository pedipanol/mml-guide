# Advanced info



## Tempo, TimerB and Whole Note Length


**\#Zenlen \<value>** determines the tick length of a whole note. By default it's 96 ticks, meaning a halfnote will be 48 ticks, a quarter, 24 ticks and so on. It's important to know it exists, but it's dangerous to change from the default.

PMD will divide this value by each note's length, so if you set it to a value that can't be divided by a length you used, it'll result in a compilation error.

This will also affect how the Tempo and Timer commands affect the speed of the song.

>Trivia: Zenlen is an abbreviation of "全音符(zen onbu) Length", meaning whole note length.

**\#Tempo \<value>** will change TimerB to match how many times a 48 tick cycle will repeat in a minute approximately. By default this makes it so the value has to be half the actual tempo. 

If you set \#Zenlen to 192, this will make the tempo value be closer to the actual tempo, but will also increase the imprecision. Sticking to the default is recommended.

**\#Timer \<value>** will change the speed value of TimerB directly.

>Since  \#Tempo is tied to this 48 tick cycle, if your \#Zenlen is not a multiple of 48 cycles, it's better to use \#Timer instead. And use the following formula
>
>`TimerB = 256 - 10800000 / (bpm * 13 * Zenlen)`
