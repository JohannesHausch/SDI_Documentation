# File cloud 

## Set up a Nextcloud server

1. First go to the Direktory where we want to install next cloud with `cd /var/www`.

2. Now download Nextcloud with

```
# wget https://download.nextcloud.com/server/releases/latest-22.tar.bz2
--2023-07-26 17:37:15--  https://download.nextcloud.com/server/releases/latest-22.tar.bz2
Auflösen des Hostnamens download.nextcloud.com (download.nextcloud.com)… 95.217.64.181, 2a01:4f9:2a:3119::181
Verbindungsaufbau zu download.nextcloud.com (download.nextcloud.com)|95.217.64.181|:443 … verbunden.
HTTP-Anforderung gesendet, auf Antwort wird gewartet … 200 OK
Länge: 131409017 (125M) [application/x-bzip2]
Wird in »latest-22.tar.bz2« gespeichert.

latest-22.tar.bz2   100%[===================>] 125,32M  59,7MB/s    in 2,1s    

2023-07-26 17:37:17 (59,7 MB/s) - »latest-22.tar.bz2« gespeichert [131409017/131409017]


```
3. Extract the downloaded files.

```
# tar -xvf latest.tar.bz2
nextcloud/
nextcloud/index.php
nextcloud/ocs-provider/
nextcloud/ocs-provider/index.php
nextcloud/ocs/
...
nextcloud/COPYING
nextcloud/config/
nextcloud/config/CAN_INSTALL
nextcloud/config/config.sample.php
nextcloud/config/.htaccess
```

4. Remove `latest.tar.bz2` with `rm latest.tar.bz2`.


5. Add permissions to Nextcloud directory.
```
# chown -R www-data:www-data /var/www/nextcloud/
# chmod -R 755 /var/www/nextcloud/
```

6. Add the nextcloud directory Host to our config:

```
# nano apache2-doc.conf 


Alias /nextcloud "/var/www/nextcloud/"

<Directory "/var/www/nextcloud/">
    Require all granted 
    AllowOverride None 
    Options Indexes Multiviews

    <IfModule mod_dav.c>
      Dav off
    </IfModule> 
</Directory>
```

7. Restart apache2.

8. Go to console and type mySQL and run that SQL to create a new Database:

```
CREATE USER 'nextcloud_user'@'localhost' IDENTIFIED BY 'qwertz';
CREATE DATABASE nextcloud;
GRANT ALL PRIVILEGES ON nextcloud.* TO 'nextcloud_user'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```
9. NextCloud Installation and server configuration.


```{image} ./images/nextcloud_01.png
:alt: Search editor
:width: 100%
:align: center
```


10. Now it should look like this:

```{image} ./images/nextcloud_02.png
:alt: Search editor
:width: 100%
:align: center
```

## Integrate LDAP authentication


1. Go to your APPs and activate LDAP user and group backend.

```{image} ./images/nextcloud_03.png
:alt: Search editor
:width: 100%
:align: center
```

2. Go to Settings and click on LDAP, there you can integrate the LDAP to authenticate the users, we use `ldap1.hdm-stuttgart.de` for this.

```{image} ./images/nextcloud_04.png
:alt: Search editor
:width: 100%
:align: center
```

```{image} ./images/nextcloud_05.png
:alt: Search editor
:width: 100%
:align: center
```

3. Now we can Open NextCloud with our student ID and password.


```{image} ./images/nextcloud_06.png
:alt: Search editor
:width: 100%
:align: center
```
```{image} ./images/nextcloud_07.png
:alt: Search editor
:width: 100%
:align: center
```

