# Pulse Audio

Using Pulse Audio to control the volume of only Shairport-Sync application while leaving the system volume alone.  This allows an announcement to be made by an MPD instance running on the same RPi_Server.  The ability to remotely control the AirPlay2 stream (pause or change volume) is currently not working due to a [known issue](https://github.com/mikebrady/shairport-sync/issues/1858) in an Apple update.  My work around is to duck the Shairport-Sync volume using Pulse Audio

Features that are important:
* Ability to control the volume of each individual application

Installation took some troubleshooting to get Pulse Audio running in system mode on this headless system and to set the various user groups to allow it to detect and report the currently playing application (i.e. sink-input).  This installation is set up for passing raw TCP commands.  However, I am currently using SSH to get to the full `pactl` command line tool.

### Install Libraries
Didn't orginally have this one and recieved and while audio functioned the TCP interfaces weren't working.  

`apt install at-spi2-core`

### Install Pulse Audio (System Mode)
Followed steps 3 through 13 in these [instructions](https://github.com/FutureProofHomes/wyoming-enhancements/blob/master/snapcast/docs/2_install_pulseaudio.md)  

At this point Shairport-Sync was running and accepting an AirPlay stream, but no audio was coming out.  
Set `output_backend = "alsa"` in the `etc/Shairport-Sync.conf` file.  Apparently this helps avoid conflicts.  
  
Audio output on the 3.5mm headphone jack was now working, the `pactl` command was showing no active sink, source, and sink-inputs event while audio was playing.  Needed to make sure everything had the right permissions

`sudo adduser shairport-sync pulse`
`sudo adduser shairport-sync pulse-access`
`sudo usermod -a -G audio pulse`

To get PulseAudio to work with TCP needed to add the following.

`sudo systemctl start avahi-daemon.service`
`load-module module-cli-protocol-tcp port=4712` in the `etc/pulse/system.pa` file.

When running PulseAudio in system mode the `system.pa` is used rather than the `default.pa`.

