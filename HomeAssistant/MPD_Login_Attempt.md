# MPD - Chime_tts - Login attempt or request with invalid authentication

I was plagued with the dreded "Login attempt or request with invalid authentication" error notification in Home Assistant whenever I used Chime_tts to play a TTS message via [MPD_2](/BasicSystemOverview.md).
  
My Home Assistant Log and Notifications were filling up with errors like these.
  
```
[homeassistant.components.http.ban] Login attempt or request with invalid authentication from 192.###.#.# (192.###.#.#).
Requested URL: '/media/local/sounds/temp/chime_tts/8db61d11be9faee2a822ea895508959f.mp3?authSig=<key>.(Music Player Daemon 0.24.4)
```

```
[homeassistant.components.http.ban] Login attempt or request with invalid authentication from 192.###.#.#(192.###.4#.#).
Requested URL: '/media/local/sounds/temp/chime_tts/cover.png'. (Music Player Daemon 0.24.4)
```
  
Examples of others with the issue:
* https://github.com/home-assistant/core/issues/113855
* https://community.home-assistant.io/t/mpd-login-failed-ban-ip-in-the-end/815995
* https://github.com/home-assistant/core/issues/66751
* https://github.com/home-assistant/core/issues/69827

#### Error 1
It took a lot of digging around on the web to find the root of the first problem.  It comes down to MPD trying to reload the last played and now deleted Chime_tts temporary mp3 file when the Home Assistant media_player entity was activated.

The solution in my setup was to use [homeassistant-ssh](../HomeAssistant/homeassistant-ssh.md) to send a `mpc clear` command **before** activating the MPD media_player in my Home Assistant script. This ensures that when the MPD media_player wakes up it doesn't try to reload the last played mp3 and its artwork.

One time there was a MPD http 401 error that was being sent to Home Assistant and looked very similar since in included the file path.  MPD played the requested TTS message while still presenting the error string.  The MPC `clearerror` command resolved this issue.  It has not happened again since.

Note:  The Home Assistant temporary auth keys included in the mp3 file path apparently expire after a day.  

#### Error 2
There is a known issue with MPD generating this error when try to load the cover art associated with a temporary Chime_tts mp3 file.

I have made sure the RPi_Server running the MPD instance has appropriate access in Home Assistant.
```yaml
homeassistant:
  auth_providers:
    - type: homeassistant
    - type: trusted_networks
      trusted_networks:
        - 192.###.#.# <ip address of RPi_Server>
  allowlist_external_dirs:
    - "/media"
```
Probably more importantly I have turned on the Chime_tts option to include cover art in its temporary mp3s.  This option was added due to this issue.
  
<img width="581" height="307" alt="Screen Shot 2025-12-14 at 4 16 12 PM" src="https://github.com/user-attachments/assets/58136445-3b1d-4a12-b2d0-024d21b79f3c" />
