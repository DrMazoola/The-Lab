# Onkyo TX-SR605 RI via ESPHome
Setup an ESP32 via ESPHOME to send Onkyo Remote Interactive (RI) commands to automate basic control functions.
  
Features that are important:
  * Integration with Home Assistant

### References:
  * https://github.com/docbender/Onkyo-RI
  * http://fredboboss.free.fr/articles/onkyo_ri.php
  * https://nuxx.net/blog/2024/08/19/onkyo-ri-for-esphome-home-assistant/
  
### Hardware:
  * ESP-WROOM-32 Module
  * 3.5mm TS female plug
  * 3.5mm TS male-to-male patch cable
  * 1000 ohm resistor

The ESP32 GPIO output pin is wired to the tip connector of the 3.5mm TS jack with a 1000 ohm resistor in-line.  This is to limit the current and prevent potential shorts.

The ground pin of the ESP32 is wired to both the ring and shield of the 3.5mm TS jack.  The Onkyo RI protocol only uses two wires.  This step is needed when using 3-wire or stereo jacks and cables.

The Onkyo TX-SR605 must be set to use the RI DOCK.  This is done by pressing and holding the TAPE botton on the front of the reciever.  It will cycle through the options `TAPE -> MD -> CDR -> DOCK -> (repeats)`.  Select the DOCK option.

The analog sound source is connected to the TAPE in RCA connectors on the back of the receiver.  In this case it is signal from the [RPi_Server](/RPi_Server/rpi_server.md).

### Onkyo RI Protocol:
The references provide additional details on the RI protocol.  There is a message header followed by a 12-bit message and a trailer.  
  
The header is 3ms high then 1ms low.
The tail is 1ms high then a gap of at lease 20ms.  

### ESPHome YAML:
Using the [Remote Transmitter](https://esphome.io/components/remote_transmitter/) component in conjunctio with the [Button](https://esphome.io/components/button/) to expose buttons in Home Assistant to perform basic control functions.  
  
Because we already have the raw binary message information it is straight forward to use the remote transmiter's raw data functionality.

The raw code values are positive for output high and negative for output low.  The number is the duration in milliseconds.  

The header is therefore `3000,-1000`
The tail is `1000,-7000`
  
The hex codes in the refereces can be converted to binary to generate the 12-bit message.

Below is an example to create a button in Home Assistant that will display the `TEST 5-00` on the SR605 display.
  
```
remote_transmitter:
  pin: GPIO##
  carrier_duty_percent: 100%

button:
  - platform: template
    name: Test 5-00
    id: test_button
    on_press:
      - remote_transmitter.transmit_raw:
          code: [3000,-1000,1000,-1000,1000,-2000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-7000]
```

### RI Codes

#### TEST 5-00
| Command | Hex Code | Binary String |
| --- | --- | --- | 
| TEST 5-00 | 0x43F | 0100 0011 1111 |

`code: [3000,-1000,1000,-1000,1000,-2000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-7000]`

#### ON to GAME/TV
| Command | Hex Code | Binary String |
| --- | --- | --- | 
| Turn On + GAME/TV | 0x1AF | 0001 1010 1111 |

`code: [3000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-2000,1000,-2000,1000,-1000,1000,-2000,1000,-1000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-7000]`

#### ON to TAPE
| Command | Hex Code | Binary String |
| --- | --- | --- | 
| Turn On + TAPE | 0x07F | 0000 0111 1111 |

`code: [3000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-2000,1000,-7000]`

#### Swich to GAME/TV
| Command | Hex Code | Binary String |
| --- | --- | --- | 
| Input GAME/TV | 0x1AO | 0001 1010	0000 |

`code: [3000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-2000,1000,-2000,1000,-1000,1000,-2000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-7000]`

#### Switch to TAPE
| Command | Hex Code | Binary String |
| --- | --- | --- | 
| Input TAPE | 0x070| 0000 0111 0000 |

`code: [3000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-2000,1000,-2000,1000,-2000,1000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-7000]`

#### STANDBY
*Note: Only works when in GAME/TV mode.*

| Command | Hex Code | Binary String |
| --- | --- | --- | 
| Standby | 0x1EA| 0001 1010 1110 |

`code: [3000,-1000,1000,-1000,1000,-1000,1000,-1000,1000,-2000,1000,-2000,1000,-1000,1000,-2000,1000,-1000,1000,-2000,1000,-2000,1000,-2000,1000,-1000,1000,-7000]`