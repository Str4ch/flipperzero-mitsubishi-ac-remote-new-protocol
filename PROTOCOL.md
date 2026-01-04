# Mitsubishi AC IR remote protocol documentation


## Overview

This document describes a Mitsubishi Air Conditioner infrared remote control protocol, reverse-engineered from a IR capture recorded using a Flipper Zero.

The protocol:

Transmits the entire AC state in a single frame

Is stateless on the receiver side (no ACK, no repeats observed)

Сompatible models:
- SRK20ZSP-S
- SRK25ZSP-S
- SRK35ZSP-S
- SRK45ZSP-S

## Timing Definitions

It consist of 179 samples and we can select 5 distinct values.

HVAC_MITSUBISHI_HDR_MARK ~3180 - Header mark

HVAC_MITSUBISHI_HDR_SPACE ~1620 - Header space

HVAC_MITSUBISHI_BIT_MARK ~420 - bit mark, used to indicate a bit

HVAC_MITSUBISHI_ONE_SPACE ~1175 - Logical 1 space

HVAC_MITSUBISHI_ZERO_SPACE ~390 - Logical 0 space

A signal is ending with bit mark.

## Raw signal
This is an example of a typical Mitsubishi AC remote IR signal(this signal was recorded using flipper zero):


```
frequency: 38000
duty_cycle: 0.330000
3188 1617 424 405 394 1178 424 377 422 406 394 1178 424 378 421 1178 423 377 422 405 394 1177 425 1177 424 1178 424 378 421 1179 423 404 395 1178 424 1176 424 1178 423 405 395 405 394 406 394 405 394 1179 423 1176 425 377 422 1178 424 1177 424 377 423 405 394 1179 423 405 394 406 394 1178 424 377 422 405 394 1179 422 1177 424 405 394 1178 424 1179 424 1176 425 405 394 1180 425 1175 426 1177 425 1176 425 1177 424 1177 424 405 395 1178 423 405 394 406 394 405 394 406 394 405 394 406 394 1177 425 1178 423 1178 423 404 395 406 394 1177 425 1176 425 1177 424 378 421 406 394 405 394 1178 424 1176 425 378 422 405 394 406 394 406 394 1178 424 1177 425 376 423 1179 423 376 423 1178 424 377 422 1178 423 405 395 405 395 1177 424 377 422 1177 424 377 423 1177 424
```



## Decoding Process

* Discard header (HVAC_MITSUBISHI_HDR_MARK & HVAC_MITSUBISHI_HDR_SPACE)

* Remove all bit marks

* Group into bytes

* Convert to least significant bit version

#### MSB-first:

 0: 01001010

 1: 01110101

 2: 11000011

 3: 01100100 

 4: 10011011

 5: 10111111

 6: 01000000

 7: 11100111

 8: 00011000

 9: 01101010

10: 10010101

## Normalized Bytes (LSB-first)

 0: 01010010 -\
 
 1: 10101110 -|
 
 2: 11000011 -| - Identifier bytes
 
 3: 00100110 -|
 
 4: 11011001 -/
 
 5: 11111101 - bitwise reverse of byte 6
 
 6: 00000010 - vane postion(part 1)
 
 7: 11100111 - bitwise reverse of byte 8
 
 8: 00011000 - first 3 bits are responsible for fan speed, next 2 for vane position(part 2)
 
 9: 01010110 - bitwise reverse of byte 6

10: 10101001 - first 4 bits for temperature, last for AC mode

## How it works
### Temperature, **10th** byte:

Temperature range: 18 - 32

x - value of first 4 bits of the last byte, representing the temperature

x = Temperature - 17

i.e.:

tmp = 32 => x = 15 = 0b1111 => 10th byte is 1111xxxx

tmp = 24 => x = 7 = 0b0111 => 10th byte is 0111xxxx

---------------

### AC Modes, **10th** byte:

```xxxx0001``` - AC off

```xxxx1000``` - Auto

```xxxx1001``` - Cold

```xxxx1010```- Dry

```xxxx1011``` - Fan

```xxxx1100``` - Heat

---------------

### Fan speed, **8th** byte:

```000xxxxx``` - Auto fan speed

```010xxxxx``` - Fan speed 1

```011xxxxx``` - Fan speed 2

```100xxxxx``` - Fan speed 3

```110xxxxx``` - Fan speed 4

```111xxxxx``` - Silent fan speed

---------------

### Vane:

Vane Auto:

**6th** byte:
```00000000```

**8th** byte:
```xxx00xxx```

---------------
Vane H1:

**6th** byte:
```00000000```

**8th** byte:
```xxx11xxx```

---------------
Vane H2:

**6th** byte:
```00000010```

**8th** byte:
```xxx00xxx```

---------------

Vane H3:

**6th** byte:
```00000010```

**8th** byte:
```xxx01xxx```

---------------

Vane H4:
**6th** byte:
```00000010```

**8th** byte:
```xxx10xxx```

---------------

Vane H4:

**6th** byte:
```00000010```

**8th** byte:
```xxx11xxx```

---------------

Vane Auto Move:

**6th** byte:
```00000000```

**8th** byte:
```xxx10xxx```






