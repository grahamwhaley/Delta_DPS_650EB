# Delta DSP-650EB PSU info

> **WARNING, DANGER OF DEATH**
>
> Note - if you are going to play around with mains powered switched mode power supplies, please
> only do so if you know what you are doing. By their nature, they rectify and store mains level
> voltages internally, so there is a strong chance there is >300v **DC** sat on some capacitors,
> even after you have powered them off and unplugged them. This can also be present on some of the
> naked internal metalwork, such as the heatsinks. This **can kill you**. Take note, and do this
> at your own risk.
>
> All information here is for information only. You have been warned!

I have a pair of Delta DPS-650EB server switch mode power supplies. These came as a pair in a 
Research Machines 'RM Dualserv', which is so old, and tbh, large and loud, it is no longer any use
to anybody. However, each of those PSUs can kick out a healthy 54A of 12v each, and that might be
of use to me.

## **Update** How to power up!

I now know how to power these up! I didn't discover the details - I found somebody on eevblog
who appeared to have powered one up, and they kindly shared the details. You can find that
thread [here](https://www.eevblog.com/forum/projects/hp-hstns-pl11-over-voltage-protection-voltage-increase/msg3549625/#msg3549625).

Bottom line, connect pins T21 (PSKILL?) and T20 (#PSON) to GND. I used 1kohm resistors to do this,
but in the original they were just direct shorts. If you are going to use one of these signals as
an on/off switch then use T20/#PSON.

Note, after a few seconds the fan spins up fairly fast, and does not decrease, even with no load
attached. These are probalby not the quietest PSUs out there.

Originally I did some digging and tried to see if I could get the units to power up outside of
the server.  Bottom line answer was **no**. During my 'probing around' (where I had really passed
the point of caring) I think I damaged one of my units, as now even with the above two pins
connected it clicks and goes into an 'orange LED' error mode.

I'll document what I know here just in case it saves somebody else some time.

## Overview

The RM Dualserv comes with dual redundant PSUs. They are moderately compact, heavy, Delta switch mode
PSUs, each capable of delivering 12v at 54A as their main output. They are rated for 655W or
thereabouts.

![DPS650EB](./images/DPS650EB.JPG)

At one end the unit has the 3pin IEC input connector and the 3 status LEDs. At the other is the
fan and the output edge connector.

The edge connector has the fairly normal double sided setup, with two large strips for the GND
and +12V (presumably), and 15 single 'pins' (7 on the bottom and 8 on the top, unusually).

![DPS650EB connector](./images/DSP650EB_connector.JPG)

## What I tried

Reading around (and if you are messing with this stuff, then you need to read the
[rcgroups posts](https://www.rcgroups.com/forums/showpost.php?p=11640787&postcount=7), there are
normally a couple of pins you need to ground to get similar units to boot. They will be PSON (to turn
the unit on), and PSKILL (which indicates if the unit is fitted to a server rack or not for instance).

Searching around the nearest 'equivalent' I could find was a reference to a 750XB unit, that has the
same number of pins and a similar 'look'. That unit appears to only have a PSON. Some details can
be found in the [relevant Intel manual](https://www.rcgroups.com/forums/showpost.php?p=11640787&postcount=7)
and also in [this useful post](https://www.rcgroups.com/forums/showpost.php?p=11640787&postcount=7).

However, testing around the pins with a 1kohm resistor to ground, and then a 330ohm resistor, and then
just a plain short produced no results, apart from I think I may have found the +5vsb line, as touching
that to ground with a short caused the PSU to go into a reset clicking cycle until I removed the link.

## What worked

Ultimately, searching around I found that the RM Dualserv was fitted with a Delta RPS-650 'cage', that
looks to transform the 650EB pinouts into a more standard 'ATX' style power supply:

![RPS-650 cage](./images/RPS-650.JPG)

That cage contains some reasonably complex extra 'gubbins', mostly I suspect as it needs to take
the 12v and convert it into +3v3 and +5v at 24A, so has a few extra reasonably chunky voltage
regulators in it. It also must contain the power on circuitry.

I put one of the 650EBs into the frame, and wired the green 'power on' line on the main ATX connector
to ground, and voila, out came 12v.

## Pinout

The 'pins' compose of edge connector pads on both sides. They are labelled partly on the PCB as
'T' and 'B' numbers - let's take those to be **T**op and **B**ottom. The labelling starts at T1-T25
and then continues back on the other side as B26-B50.

The below table tries to represent what I've found so far, which is not much:

| Pin | Function |
| --- | -------- |
| T1  | +12v |
| T2  | +12v |
| T3  | +12v |
| T4  | +12v |
| T5  | +12v |
| T6  | +12v |
| T7  | +12v |
| T8  | +12v |
| T9  | GND |
| T11 | GND |
| T12 | GND |
| T13 | GND |
| T14 | GND |
| T15 | GND |
| T16 | GND |
| T17 | GND |
| T18 | +5sb ??? (shorting to GND resets PSU) |
| T19 | |
| T20 [short pin] | #PSON ? |
| T21 | PSKILL ?. Possibly fan control. 330ohm to T20 slows fan down, even when 12V not powered up |
| T22 | |
| T23 | |
| T24 | |
| T25 | |
| B26 | |
| B27 | |
| B28 | |
| B29 | |
| B31 | |
| B32 | |
| B33 | GND |
| B34 | GND |
| B35 | GND |
| B36 | GND |
| B37 | GND |
| B38 | GND |
| B39 | GND |
| B40 | GND |
| B41 | GND |
| B42 | +12v |
| B43 | +12v |
| B44 | +12v |
| B45 | +12v |
| B46 | +12v |
| B47 | +12v |
| B48 | +12v |
| B49 | +12v |
| B50 | +12v |

Note, as far as I can see there are *no* 'No Connection' (NC) pins on the header - here are some closeup
pictures:

![650EB top of connector](./images/closeup_top.JPG)

![650EB bottom of connector](./images/closeup_bottom.JPG)

## Ultimately 

These are *loud*. Even with no load, when powered up in the RPS-650, a single unit ran its fan up
to full speed, or so it seemed. Too loud for me to have living in my den/office. Maybe if I find a
need for a big PSU far far away (in the garage for instance), it might be workable.

If you do manage to get one of these powered up, inside you will find a couple of small potentiometers
that will probably allow you to alter the output voltage, if you need more than 12v. One of the
pots (I will bet the on on the RHS) will do that. The other alters the over current trip level I suspect.

![650EB internal pots](./images/650EB_internals.JPG)
