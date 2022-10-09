# pedipanol's guide to basic MML syntax

Welcome! This guide aims to familiarize people with the general concepts of MML that will apply to most different formats out there. This is more about general information that (at least in concept) will apply to 90% of the MML formats out there, so even though each format will diverge in its own, this guide at the very least will help you get the gist of it to the point you can figure out the specifics on your own.

## Part 1: A bit of History

Standing for Music Macro Language was introduced do the general public as the internal music driver in Microsoft BASIC for japanese personal computers in the early 80's. It consisted of a mix of the note sequence format that would evolve into what it is today with BASIC commands for operating sound effects and the like.

The note sequence format was designed **based on sheet music** sequencing. And this was a key element for it getting popular in Japan, as essentially musicians were just writing sheet music in plain text form, and so the **same baseline framework started being used for most drivers** (consumer-grade or not) going forward.

As trackers were introduced for western machines, it took until the late 2000's with **LSDJ**   and **Famitracker** for they to become widely known in the japanese chiptune scene. With the **MSX** seeing some success (mainly in areas Commodore didn't dominate the 8bit market), MML was known to some degree.

In 2001 **Music Compiler Kit** (also known as mck) for the NES/Famicom was created in japan, and with Jake Kaufman himself translating its manual in english, awareness of the MML format in the west increased, raising even more as tools based around mck's format for other systems (like HusiC) got developed.

With Famitracker becoming a robust software, many mck users dropped it off, with the same being done for other formats as other trackers arose. But it doesn't mean the format died down, as it **still is a key piece in the japanese chiptune scene**. And in the west, romhacking is the main thing keeping it alive with **AddMusicK** for Super Mario World, alongside enthusiasts for the format and games like **Touhou** who want to go the extra mile.

## Part 2: Why Learn MML?

The main reason most western chiptuners learned MML was either because **it was the only option** or because the trackers **weren't as feature-packed** as the MML counterpart. For example, lots of people I've talked to that used PPMCK (a mck fork) after Famitracker came out only did it because **there was no way of using multiple expansion chips at the same time** yet.

But recently objective reasons for learning MML have been growing thinner and thinner, and that's a great thing! It just means that people now have options of how to make music. For example, tildearrow has been doing god's work in **Furnace** to address so many chips that never got a tracker before, and even though native driver export is not available for most of them yet (as of writing this), things look promising moving forward.

Compared to trackers and whatnot, MML imposes a rough learning curve: taking away the conveniences of modern software and having a workflow that makes it **pivotal** for the one who writes the sequence **to understand how things are interpreted** so to not go wrong once it's played. Because of this (and being text-based), a lot of people misjudge it as being **"the same as programming"**, but there's a way better thing to compare to, that works almost in **the same way** and people have been using for literally **centuries** before computers came along: **Sheet Music**.

The purpose of this section isn't as much to provide an answer to the question, but to ask back **"Why Not?"**. Every kind of sequencing format has its benefits one can point out but it always boils down to personal preference. MML is just another format.

Like many I also started learning MML because there was no other option: I was really into the PC-98 Touhou games and wanted to make the same style of music that ZUN did, and there was no tracker for the YM2608 out there yet. But now I have not only 1 but 2 trackers to choose from... and I still cling to MML? Why? **Because I find it more fun**.

If I were to explain why it's because, _for me_, using MML is a natural consequence of the 2 things that I love about chiptune:

1. The marvel of making music for the old videogames I loved to play:

In MML I'm using the same or a much more similar toolset that a lot of composers I really like used back then than trackers.

2. Limitations are fun:

Modern trackers allow for full control and milimetric precision of the soundchip. While MML allows you to do that (see Tappy's witchery with the YM2151), its workflow is more about telling the **driver** what to do instead of the **soundchip**, so it restricts you just enough to make it a different experience but not as much to ruin it.

I write this guide in hopes that more people will like it too and find their own reasons to have fun with it. and so that the lack of information or lack of a simpler guide won't be a barrier for you to get into it.
