# Shairport-Sync
  
Many thanks to [parautenback](https://github.com/parautenbach)
  
Used to stream music, Audible, podcasts to the whole house audio system.

Features that are important:
* AirPlay2
* Allows me to use my preferred apps and their features
* Allows me to output AirPlay2 audio to the same audio jack as the MPD instances
* Provides a way to lower the volume while an announcment is made by one of the MPD instances

Repository: [hass-shairport-sync](https://github.com/parautenbach/hass-shairport-sync)

The installation on the [RPi_Server](../rpi_server.md) was relatively straight forward following the instructions.  
The MQTT remote control feature currently doesn't work due to a change in Apple's protocol.  This would be very handy to pause the sound source while a TTS announcment is made.  The current work around is to use [PA_Volume](../PA_Voume.md) and [Pulse Audio](../Pulse_Audio.md) to lower the volume of Shairport-Sync while the announcement is made.

Installation generally followed the instruction, but there are some customizations.

### Build and Install [NQPTP](https://github.com/mikebrady/nqptp)

```
$ git clone https://github.com/mikebrady/nqptp.git
$ cd nqptp
$ autoreconf -fi
$ ./configure --with-systemd-startup
$ make
# make install
# systemctl enable nqptp
# systemctl start nqptp
```

### Install Libraries
I needed to add the libmosquitto-deb library after getting an error message on the first build.
```
# apt update
# apt upgrade # this is optional but recommended
# apt install --no-install-recommends systemd-dev
# apt install --no-install-recommends build-essential git autoconf automake libtool \
    libpopt-dev libconfig-dev libasound2-dev avahi-daemon libavahi-client-dev libssl-dev libsoxr-dev \
    libplist-dev libsodium-dev libavutil-dev libavcodec-dev libavformat-dev uuid-dev libgcrypt-dev libmosquitto-dev xxd
```

### Build and Install [Shairport-Sync](https://github.com/mikebrady/shairport-sync)
* Added the --with-mqtt-client, --with-metadata flags per the instructions
* Added the --with-systemd flag to run on my headless system
* Added the --with-pa flag to work with PulseAudio (https://github.com/mikebrady/shairport-sync/issues/1596)
```
$ git clone https://github.com/mikebrady/shairport-sync.git
$ cd shairport-sync
$ autoreconf -fi
$./configure --sysconfdir=/etc --with-alsa \
    --with-soxr --with-avahi --with-ssl=openssl --with-systemd --with-airplay-2 --with-mqtt-client --with-metadata --with-systemd  -with-pa
$ make
# make install
# systemctl enable shairport-sync
# systemctl start shairport-sync
```
