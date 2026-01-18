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
<u>Command      Hex Code    Binary String</u>
TEST 5-00       0x43F       0100	0011	1111