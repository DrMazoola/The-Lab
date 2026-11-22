# Pi-hole Server 

Running Pi-hole on a Raspberry Pi.  

#### Hardware  
  * Raspberry Pi 4

#### Software  
  * [Pi-hole](https://pi-hole.net)
  * Mosquitto 

#### Process 

##### Pi-hole Installation 
Standard, followed instruction on Pi-hole.net. 

##### Pi-hole Block Lists 
```
https://raw.githubusercontent.com/StevenBlack/hosts/master/hosts
https://big.oisd.nl
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/tif.txt
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/pro.txt
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/popupads.txt
https://raw.githubusercontent.com/kboghdady/youTube_ads_4_pi-hole/master/youtubelist.txt
https://adaway.org/hosts.txt
https://v.firebog.net/hosts/AdguardDNS.txt
https://v.firebog.net/hosts/Prigent-Ads.txt
https://v.firebog.net/hosts/Prigent-Crypto.txt
https://raw.githubusercontent.com/StevenBlack/hosts/master/alternates/fakenews-gambling-social/hosts
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adguard/dns-rebind-protection.txt
https://cdn.jsdelivr.net/gh/hagezi/dns-blocklists@latest/adblock/pro.plus.txt 
```

##### Pi-hole Update Script
```
placeholder
```

##### Mosquito Installation

Install the broker 
```
sudo apt update
sudo apt install -y mosquitto mosquitto-clients
sudo systemctl enable --now mosquitto
```

Allow Home Assistant to connect.  Create password and config files.
```
sudo mosquitto_passwd -c /etc/mosquitto/passwd haupdates
sudo nano /etc/mosquitto/conf.d/ha.conf
sudo chown mosquitto:mosquitto /etc/mosquitto/passwd
sudo chmod 600 /etc/mosquitto/passwd
```

Restart the service
```sudo systemctl restart mosquitto```
