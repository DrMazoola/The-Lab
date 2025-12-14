# PA_Volume

Many thanks to [rhass80](https://github.com/rhaas80).  

Using this to simplify making volume changes to individual applications using PulseAudio on a headless Raspberry Pi.

Features that are import:
* Remembers PulseAudio sink-inputs by name, even when they are currently not playing (and not listed via `pactl`).
* Simple commands that are easy to call via SSH.

Repository: [pa_volume](https://github.com/rhaas80/pa_volume)

Using homeassistant-ssh to send the commands.  *Homeassistant-ssh* is set up to send a predetermined command when a Home Assistant button is pushed.  This requires a two step process to set the PulseAudio sink-input volume.  See the homeassistant-ssh page for using a Home Assistant template to pass an argument to the ssh command.

1. Use a Home Assistant number helper to selected the desired volume level (0-100).
2. Activate the *homeassistant-ssh* button.
