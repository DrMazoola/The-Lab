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

It took a lot of digging around on the web to find the root of the first problem.  It comes down to MPD trying to reload the last played and now deleted Chime_tts temporary mp3 file when the Home Assistant media_player entity was activated.

The solution in my setup was to use [homeassistant-ssh](../HomeAssistant/homeassistant-ssh.md) to send a `mpc clear` command **before** activating the MPD media_player in my Home Assistant script. This ensures that when the MPD media_player wakes up it doesn't try to reload the last played mp3 and its artwork.
