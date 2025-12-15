# Pulse Audio

Under Construction

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
