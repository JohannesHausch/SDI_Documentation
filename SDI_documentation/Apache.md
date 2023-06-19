# Apache Webserver

## Create DNS entries for the pages
To make your website accessible through a URL, you need to assign an IP address to the URL.

```
root@sdi04a:~# nsupdate -y $HMAC
> update add doc3.g4.sdi.mi.hdm-stuttgart.de 86400 A 141.62.75.104
> send
> update add doc4.g4.sdi.mi.hdm-stuttgart.de 86400 A 141.62.75.104
> send
> quit`
```

## Install and Start Apache2
Execute this command in your shell: `$ aptitude install apache2`

Next you have to start the apache-service `$systemctl start apache2.service` To check if everything works fine, run this command: `$ systemctl status apache2.service`.

The output should look like this:
```
● apache2.service - The Apache HTTP Server
     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese>
     Active: active (running) since Mon 2023-06-05 15:11:42 CEST; 12min ago
       Docs: https://httpd.apache.org/docs/2.4/
   Main PID: 25762 (apache2)
      Tasks: 55 (limit: 619009)
     Memory: 6.5M
        CPU: 86ms
     CGroup: /system.slice/apache2.service
             ├─25762 /usr/sbin/apache2 -k start
             ├─25764 /usr/sbin/apache2 -k start
             └─25765 /usr/sbin/apache2 -k start

Jun 05 15:11:42 sdi04a systemd[1]: Starting The Apache HTTP Server...
Jun 05 15:11:42 sdi04a systemd[1]: Started The Apache HTTP Server.
```


## Set name for adress
You need to first create a DNS entry. To do this enter and execute this code.

```
$ nsupdate -y $HMAC
> server nssdi.mi.hdm-stuttgart.de
> update add g4.sdi.mi.hdm-stuttgart.de/doc1 86400 A 141.62.75.104
> send
> update add g4.sdi.mi.hdm-stuttgart.de/doc2 86400 A 141.62.75.104
> send
> quit
```

## What happens if you move the original start html somewhere else?

The original HTML document is located at: `/var/www/html/index.hmtl`.

`$ mv index.html doc.html`

After reloading the service `systemctl reload apache2.service` the website content looks like this:

```
<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
<html>
 <head>
  <title>Index of /</title>
 </head>
 <body>
<h1>Index of /</h1>
  <table>
   <tr><th valign="top"><img src="/icons/blank.gif" alt="[ICO]"></th><th><a href="?C=N;O=D">Name</a></th><th><a href="?C=M;O=A">Last modified</a></th><th><a href="?C=S;O=A">Size</a></th><th><a href="?C=D;O=A">Description</a></th></tr>
   <tr><th colspan="5"><hr></th></tr>
<tr><td valign="top"><img src="/icons/text.gif" alt="[TXT]"></td><td><a href="doc.html">doc.html</a></td><td align="right">2023-06-05 15:11  </td><td align="right"> 10K</td><td>&nbsp;</td></tr>
   <tr><th colspan="5"><hr></th></tr>
</table>
<address>Apache/2.4.56 (Debian) Server at doc3.g4.sdi.mi.hdm-stuttgart.de Port 80</address>
</body></html>
```

## Creation of your own html root index.html
Move to the `/var/www/html/` directory and create a new file using touch. Name it `index.html` and insert some html code. Here is an example:
```
<!DOCTYPE html>
<html>
<head>
  <title>Hello world</title>
</head>
<body>
<h1>Hello world</h1>
  <p>Sample content</p>
</body>
</html>
```

## Install the Apache documentation 
Install the package apache2-doc with:

`apt install apache2-doc`

Now Acces the Apache document with /manual

## Show documentation on the webserver

Upload your current HTML documentation to the `/var/www` directory. To make sure that the web server can access the site, you will need to set the appropriate permissions. In our case, it's the `sdidoc` directory:
```
chown -R www-data:www-data /var/www/sdidoc
chmod -R 755 /var/www/sdidoc
```

Now edit the conf in `/etc/apache2/apache2.conf` and add the path to your HTML documentation:

```
Alias /g4-doc /var/www/sdidoc/_build/html

<Directory "/var/www/sdidoc/_build/html">
  Options Indexes FollowSymlinks
  AllowOverride None
  Require all granted
  AddDefaultCharset off
</Directory>
```
After Restarting Apache we can now open our Webiste and add /doc-conf to see our Documentatio

## Virtual host

To set up a virtual host for your website, you need to create a configuration file like `g4-doc.conf` in the `/etc/apache2/sites-available/` directory. You can then configure the virtual host in the created file.

```
<VirtualHost *:80>
  DocumentRoot /var/www/sdidoc/_build/html
  ServerName mi.hdm-stuttgart.de
  ServerAlias doc4.g4.sdi.mi.hdm-stuttgart.de
  ErrorLog ${APACHE_LOG_DIR}/error.log
  CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost> 
```

Use the command `a2ensite` and enter the name of the config file.

After restarting apache, you can now open your documentation under:
http://doc4.g4.sdi.mi.hdm-stuttgart.de/intro.html