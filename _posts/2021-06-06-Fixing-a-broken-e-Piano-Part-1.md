---
layout: post
title: Fixing a broken e-Piano - Part 1
date:   2021-05-20 19:20:00
cover: transparent.png
categories: posts
---
<br>
A few months ago, I was lucky enough to find someone giving away a broken e-Piano for free online. Since the offer stated `does turn on without a problem, just no sound`, I felt rather confident that the problem couldn't be too hard to fix. So, I ended up getting it and started to have a look at it at home.

Since it actually turned on and all the logic (display, sound presets, etc.) seemd to work, the first thing I tried was checking the headphone outputs. I pulled out the small PCB, cleanned the two headphone jacks with some contact cleaner and plugged in some headphones. But sadly, no sound was coming through here either. Next, I focused on the ports in the back of the piano. The two line-out jacks also didn't deliver any sound, but what was interesting, were the two MIDI connectors and the USB type B port.

{% include image.html url="/images/digital_piano/io_red_box2.png" description="I/O-Panel" %}

Most digital pianos feature a MIDI output ([https://en.wikipedia.org/wiki/MIDI](https://en.wikipedia.org/wiki/MIDI)) to send instructions to some other musical device. That means, I can hook up my laptop, using the USB port and try to play some notes. So I got an old printer cabel, connected my laptop, downloaded the first MIDI keyboard app off of the Windows App Store and....**YES**, we got sound! That means the keyboard and logic components are working just fine. That also means I just got an 88 key velocity-sensitive MIDI controller for free - *nice*. Time to open it up.

{% include image.html url="/images/digital_piano/all_pcbs3.png" description="Inside of the piano" %}

There is **a lot** of empty space in it. Only three circuit boards are immediatly visible and it seems that those are the main electronic components of the piano. The faceplate with its LCD and buttons isn't easily accessible, but since those seem to be working just fine, I might be in luck here!<br>
The smallest PCB (**Number 3**) is the one with all the ports on it. Since the MIDI interface is working just fine and there are no apperant connections back to the speakers or headphone jacks, this PCB most likely isn't the problem.

The green PCB in the middle (**Number 2**) features a large chip labled `Dream SAM9753-TQ` ([datasheet](https://blog.jamesbaber.co.uk/classenti-cdp1/datasheets/sam9753.pdf)) which, after some googling, turns out to be a 16 bit processor with some added features, specifically made for the use in such instruments. That, along with the amount of other chips on the board, and the fact, that everything is surface-mounted, makes it pretty obvious that this PCB is the central logic board of the piano. Thank god, we probably don't need to repair anything here!

That leaves us with the large brown PCB to the left (**Number 1**). First of all, all the components are through-hole devices, which is very nice for repairability. Since the transformer is connected to this board, it's also pretty clear, that it handles the power delivery.<br>
The power cable together with the power switch, the primary side of the transformer and some passive components is connected to one corener of the PCB, which has a thick white border around it. The other side of the transformer actually has four cables coming out instead of two, which means it has two instead of one secondary side. This is also confirmed by it's label telling us the yellow output runs at 10.2V 300mA, while the blue one runs at 26.3V 900mA. Those are then connected back to the PCB and into their own rectifying stage.

{% include image.html url="/images/digital_piano/amp_power3.png" description="(Don't mind the missing fuse, I took the picture later after I removed it)" %}

Since the piano is turning on (no fuses are broken) this stage of the PCB is working perfectly fine. But at least now we now know there are two distinct power lines, one at 10.2V and one at 26.3V, the higher probably feeding some amplification stage.

The main IC on this PCB is the LA4282 ([datasheet](https://www.onsemi.com/pdf/datasheet/la4282-d.pdf)), a 10W two-channel amplifier. Since the rest of the components on here (with the exception of one lonely transistor) are passive, it's pretty safe to say that this PCB serves two purposes: It handles the power distribution as well as the amplification of the logic board's generated sounds. Judging by this assumption, there should be two connections between this PCB and the logic board, and indeed there are:

{% include image.html url="/images/digital_piano/amp2cpu_2.jpg" description="Two connections between Amp-PCB and logic board" %}

While the white 5-wire connection seems to be carrying power to the logic board, the other connection actually looks like it's carrying the audio signal to the amplifier. There are three wires and mesuring with my multimeter in AC-mode (I *really* need to get an oscilliscope) reveals, there are voltage spikes when hitting a key. So I botched together a make-shift breakout point from two stereo jacks and some jumper cables, plugged this into the logic board and routed the signal through an external amplifier using an AUX-to-Cinch cable.

{% include image.html url="/images/digital_piano/cinch_breakout.png" description="Audio breakout device" %}

By doing so I effectively intercepted the signal at the entrace point to the amplifier PCB and amplified it externally. **And it actually works!** The piano plays like a charm! The problem, therefore, has to be somewhere on the amplifier part of this PCB. Since every component is through-hole and the main IC is easily available for less than 10€, that's really promising!

In the next part I'll write about what the actual problem was, how I found it and how I went about fixing it.
