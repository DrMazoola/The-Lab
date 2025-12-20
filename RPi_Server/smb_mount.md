# SMB Mount

Mount the UNAS-Pro on the RPi_Server

Features that are important:
* Facilitates file transfer via ssh
* Works together with the WebDav server to support backup features of other software on the network

### Update and Install Utilities
```
sudo apt update
sudo apt upgrade -y
sudo apt install cifs-utils
```

### Create a mount point
  
`sudo mkdir /mnt/UNAS_L`  
  
### Secure Credentials
  
`sudo nano /etc/smbcredentials`  
  
In the file add:
`username=<smb user name>`  
`password=<smb user password>`  
  
Set permissions
`sudo chmod 600 /etc/smbcredentials`  
  
### Set up FSTAB to automount on reboot
I've added two lines to fstab.  The first is to mount a smb drive as the webdav root folder
`//<NAS IP>/W /mnt/unasW cifs credentials=/root/smbcredentials,uid=www-data,gid=www-data,dir_mode=0777,file_mode=0777,nounix,x-systemd.automount 0 0`  
Note that this needed to run as root (via credential location) and give the correct ownership and permissions when mounted.  

The second line is to mount a local shared drive hosted on the NAS and mount is as the server admin  
`//<NAS IP>/L /mnt/unasL cifs credentials=/etc/smbcredentials,uid=<username>,gid=<username>,dir_mode=0777,file_mode=0777,nounix,x-systemd.automount 0 0`   

Reload the fstab file  
`sudo systemctl daemon-reload`   

Remount the drives
`sudo mount -a`  
  
