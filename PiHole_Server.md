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

##### Update Script Modification
Added the following to the beginning of the existing update script that updates first the server itself and then Pi-hole. 

```
set -eEuo pipefail

MQTT_HOST="localhost"
MQTT_USER="haupdates"
MQTT_PASS="yourpassword"
TOPIC="pi/updates/status"

report() {  # usage: report <status> <message>
  mosquitto_pub -h "$MQTT_HOST" -u "$MQTT_USER" -P "$MQTT_PASS" \
    -t "$TOPIC" -r \
    -m "{\"status\":\"$1\",\"message\":\"$2\",\"timestamp\":\"$(date -Iseconds)\"}" || true
}
```

Appended a mqtt report after every line of code that was writing to log
Example:
```
log "Removing unused packages..."
report running "Removing unused packages..."
```

Finally added the following at the very end of the script file.
```
# Report any failure automatically
trap 'rc=$?; report failed "Failed at line $LINENO (exit code $rc)"; exit $rc' ERR
```

##### Debugging 
The HA shell command has a timeout (60s) that was killing the ssh session and the script itself.  Need to make the ssh call run the script detached from the ssh session with the following:
```
shell_command:
  pi_update: >
    ssh -i /config/.ssh/id_ed25519
    -o UserKnownHostsFile=/config/.ssh/known_hosts
    -o StrictHostKeyChecking=accept-new
    youruser@PI_IP_ADDRESS
    'setsid nohup /home/youruser/update.sh > /home/youruser/update.log 2>&1 < /dev/null &'
```
