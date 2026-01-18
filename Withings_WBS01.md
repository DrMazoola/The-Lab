# Withings WBS01 WiFi Scale with Home Assistant

This project uses advanced network router settings to redirect traffic from a Withings WBS01 WiFi scale to a local webserver using PHP scripts to provide the expected responses.  Data from the scale is converted into json and forwarded to Home Assistant using MQTT.
  
Many thanks to [cv07nguyend's](https://github.com/cv07nguyend/Withings) original project.
  
   
#### Hardware
  * Raspberry Pi 4 as the web server
  * Unifi Dream Machine as the router
  * Withings WBS01 Wifi scale
  
#### Software
  * Apache2 web server
  * PHP-CGI
  * MQTT Explorer
  * Home Assistant
  
#### Process
  
##### Install Apache2 and PHP-CGI
```
#Install updates
sudo apt update
sudo apt upgrade -y
  
#Install apache and php-cgi
sudo apt install apache2 php libapache2-mod-php 

#Install mods
sudo a2enmod php8.4-cgi
sudo a2enmod actions
sudo a2enmod cgi
sudo a2enmod rewrite

sudo systemctl restart apache2
```

Not sure if this is needed...    
`sudo a2dismod php5 # this disables mod_php from the standard installation`
  
##### Setup the web site
This project is set up to have everything in the /var/www/html directory.
  
```
# create a sub-directory for the scripts
sudo mkdir /var/www/html/cgi-bin
  
# set permissions
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html
```
  
Create the index.html and info.php files into the root folder (/var/www/html).  
  
##### Install MQTT 
Install Composer
```
sudo apt install php-cli unzip git curl php-mbstring php-phar -y
cd /var/www/html
curl -sS https://getcomposer.org/installer -o composer-setup.php
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
```
  
Install MQTT Client 
`sudo composer require php-mqtt/client`


>>>>>
sudo chown -R www-data:www-data php-cgi
