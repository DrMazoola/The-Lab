# Pulse Audio

Using Pulse Audio to control the volume of only Shairport-Sync application while leaving the system volume alone.  This allows an announcement to be made by an MPD instance running on the same RPi_Server.  The ability to remotely control the AirPlay2 stream (pause or change volume) is currently not working due to a [known issue](https://github.com/mikebrady/shairport-sync/issues/1858) in an Apple update.  My work around is to duck the Shairport-Sync volume using Pulse Audio

Features that are important:
* Ability to control the volume of each individual application

Installation took some troubleshooting to get Pulse Audio running in system mode on this headless system and to set the various user groups to allow it to detect and report the currently playing application (i.e. sink-input).  This installation is set up for passing raw TCP commands.  However, I am currently using SSH to get to the full `pactl` command line tool.

### Install Libraries
Didn't orginally have this one and recieved and while audio functioned the TCP interfaces weren't working.
`apt install at-spi2-core`

### Install Pulse Audio (System Mode)
Followed steps 3 through 7 in these [instructions](https://github.com/FutureProofHomes/wyoming-enhancements/blob/master/snapcast/docs/2_install_pulseaudio.md)

-------------------------------------------------------------------------------

apt install at-spi2-core

follow these instructions for installing pulseaudio at the system level
https://github.com/FutureProofHomes/wyoming-enhancements/blob/master/snapcast/docs/2_install_pulseaudio.md

pulseaudio stopped shairport from making sound
	In shairport-sync.conf, set the alsa backend to this specific device instead of "default" to avoid conflicts.


Needed these access to get pulseaudio to see shairport playing
sudo adduser shairport-sync pulse
sudo adduser shairport-sync pulse-access 



pulseaudio needs to be running as user
 systemctl --user enable pulseaudio.service



sudo usermod -a -G audio pulse
sudo systemctl start avahi-daemon.service


added the following to the /etc/pulse/system.pa file (this got the modules to load as system)!!
	load-module module-cli-protocol-tcp port=4712

	load-module module-native-protocol-tcp port=4713 auth-ip-acl=192.0.0.0/8
	load-module module-zeroconf-publish
