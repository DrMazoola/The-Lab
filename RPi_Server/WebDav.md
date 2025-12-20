# WebDav

Local WebDav server providing access to a folder on the UNAS to facilitate file transfer, file syncing, and backups for applications that only support WebDav.
  
Features that are important:
* Allows local syncing of DevonThink database
  
### Update, Install, and Start
```
sudo apt update
sudo apt upgrade -y
sudo apt install apache2 -y
sudo systemctl enable apache2
sudo systemctl restart apache2
```
### Enable WebDav Modules
```
sudo a2enmod dav
sudo a2enmod dav_fs
sudo a2enmod dav_lock
sudo a2enmod auth_digest
```
### Create the WebDav Directory
This is done on the NAS.

### Directory for DAVLock
```
sudo mkdir -p /usr/local/apache/var/
sudo chown www-data:www-data /usr/local/apache/var
sudo chmod 766 /usr/local/apache/var
```
### Apache2 Config
Edit the default Apache configuration file (/etc/apache2/sites-available/000-default.conf) to include the WebDAV directives.  

First line 
`DavLockDB /usr/local/apache/var/DavLock`

Before the closing of the VirtualHost section
```
Alias /webdav /mnt/UNAS_W/
<Location /webdav>
    DAV On
    AuthType Digest
    AuthName "WebDAV"
    AuthUserFile /etc/apache2/webdav.passwd
    Require valid-user
    Options Indexes FollowSymLinks
</Location>
```
With SSL
```
DavLockDB /usr/local/apache/var/DavLock

<VirtualHost *:443>
        # The ServerName directive sets the request scheme, hostname and port that
        # the server uses to identify itself. This is used when creating
        # redirection URLs. In the context of virtual hosts, the ServerName
        # specifies what hostname must appear in the request's Host: header to
        # match this virtual host. For the default virtual host (this file) this
        # value is not decisive as it is used as a last resort host regardless.
        # However, you must set it for any further virtual host explicitly.
        #ServerName www.example.com

        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/html
        
        # Available loglevels: trace8, ..., trace1, debug, info, notice, warn,
        # error, crit, alert, emerg.
        # It is also possible to configure the loglevel for particular
        # modules, e.g.
        #LogLevel info ssl:warn

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        # For most configuration files from conf-available/, which are
        # enabled or disabled at a global level, it is possible to
        # include a line for only one particular virtual host. For example the
        # following line enables the CGI configuration for this host only
        # after it has been globally disabled with "a2disconf".
        #Include conf-available/serve-cgi-bin.conf

        Alias /webdav /mnt/UNAS_W

        SSLEngine on
        SSLCertificateFile /etc/ssl/certs/tls.crt
        SSLCertificateKeyFile /etc/ssl/certs/tls.key

        <Location /webdav>
          DAV On
          AuthType Digest
          AuthName "WebDAV"
          AuthUserFile /etc/apache2/webdav.passwd
          Require valid-user
          Options Indexes FollowSymLinks MultiViews
          DirectoryIndex disabled # Crucial for clean WebDAV operation
        </Location>

</VirtualHost>
```
  
### Create User
Create a password file and add a user (replace username with your desired user).  
```
sudo htdigest -c /etc/apache2/webdav.passwd "WebDAV" <yourusername>
sudo chown www-data:www-data webdav.passwd
```
### Restart Apache2
`sudo systemctl restart apache2`

## SSL
followed these instructions  
https://gist.github.com/kaczmar2/e1b5eb635c1a1e792faf36508c5698ee  

Enable SSL Module
sudo a2enmod ssl
sudo a2ensite default-ssl  
sudo systemctl restart apache2  

