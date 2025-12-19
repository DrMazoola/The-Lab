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
`UNAS-Pro.local/L /mnt/UNAS_L cifs credentials=/etc/smbcredentials,uid=1000,gid=1000,iocharset=utf8,nofail,x-systemd.automount`  
`sudo mount -a`  
`sudo systemctl daemon-reload`  
  
