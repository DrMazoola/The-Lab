# homeassistant-ssh

Many thanks to [zhbjsh](https://github.com/zhbjsh).

Using this to monitor and send SSH commands to the RPi_Server from Home Assistant.

Features that are import:
* Simple interface for sending commands and creating Home Assistant `Sensors`.

Repository: [homeassistant-ssh](https://github.com/zhbjsh/homeassistant-ssh)

Installation and set-up is staightforward from the instructions.

These are expamples of code snippets for creating custom commands.  The first example uses a value from a Home Assistant helper in the form of a template.  The helper is used to select a desired volume and then calling the button created by this code to *apply* the new volume.
```yaml
- command: >-
    pa_volume "ALSA plug-in [shairport-sync]" {{ states
    ('input_number.sa_volume_helper') }}
  name: Set SA Volume
  key: set_sa_volume
```
   
```yaml
- command: mpc -p 6601 clear
  name: mpd2_clear
  key: mpd2_clear
```

This an example of sensor used to read back the volume of a specific `sink-input` using [pa_volume](main/RPi_Server/PA_Volume/pa_volume.md).  The grep is used to return only the digits making up the volume in the response.  If the `sink-input` name had numeric digits a more refined regex would be needed.
```yaml
- command: pa_volume "ALSA plug-in [shairport-sync]" | grep -Eo '[0-9]+'
  scan_interval: 2
  sensors:
    - type: number
      name: Shairport Volume
      key: shairport_volume
```
