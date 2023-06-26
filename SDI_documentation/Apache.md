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

## SSL / TLS Support

### Activate module
You first need to activate Apaches ssl-module:
```
a2enmod ssl
systemctl restart apache2
```
### Create SSL certificate

Step 1: Generate an RSA private key
```
$ cd CertificateAuthority/
$ openssl genrsa -des3 -out rootCA.key 2048

Generating RSA private key, 2048 bit long modulus (2 primes)
..........................................+++++
....................................+++++
e is 65537 (0x010001)
Enter pass phrase for rootCA.key: PASS_PHRASE
Verifying - Enter pass phrase for rootCA.key: PASS_PHRASE
```

Step 2: Create a root certificate
```
root@sdi04a:/CertificateAuthority# openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 730 -out rootCA.pem
Enter pass phrase for rootCA.key: PASS_PHRASE
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:DE
State or Province Name (full name) [Some-State]:Baden-Württemberg
Locality Name (eg, city) []:Stuttgart
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Hochschule der Medien
Organizational Unit Name (eg, section) []:SDI_04
Common Name (e.g. server FQDN or YOUR name) []:Gruppe_04
Email Address []:
```

Check the created certificate:
```
root@sdi04a:/CertificateAuthority# openssl x509 -in rootCA.pem -text -noout
Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number:
            5e:9b:56:0a:c4:d2:d5:1c:89:3d:63:f9:55:7f:33:4a:d5:b2:56:f1
        Signature Algorithm: sha256WithRSAEncryption
        Issuer: C = DE, ST = Baden-W\C3\83\C2\BCrttemberg, L = Stuttgart, O = Hochschule der Medien, OU = SDI_04, CN = Gruppe_04
        Validity
            Not Before: Jun 26 13:09:43 2023 GMT
            Not After : Jun 25 13:09:43 2025 GMT
        Subject: C = DE, ST = Baden-W\C3\83\C2\BCrttemberg, L = Stuttgart, O = Hochschule der Medien, OU = SDI_04, CN = Gruppe_04
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                RSA Public-Key: (2048 bit)
                Modulus:
                    00:ba:2d:eb:d0:11:46:cc:4d:44:79:24:9d:73:18:
                    f0:59:ec:4e:83:90:b0:af:b5:2f:61:ab:85:3c:eb:
                    ee:00:75:45:57:eb:82:a6:2d:35:1f:5d:d5:da:ab:
                    3f:72:9d:53:21:4d:9c:70:de:25:91:a6:78:5f:da:
                    bb:95:87:f5:1b:4c:ed:ea:f8:a1:4c:43:f3:2a:5e:
                    ef:65:7f:50:13:25:7a:c2:b3:81:a4:09:ef:5c:31:
                    fb:92:ff:10:81:7d:23:71:ed:f0:62:0c:3b:e5:80:
                    a6:da:14:4f:6b:af:01:33:f9:43:8c:a2:2a:f0:ce:
                    46:fe:51:05:fc:a4:12:d2:5a:39:dc:18:92:35:c8:
                    ff:de:d9:cb:6b:7a:5f:f4:6c:15:5d:99:81:38:ac:
                    95:ac:dd:6f:b7:0a:41:e2:66:f1:f7:e0:c9:a3:96:
                    3b:ab:c8:af:16:e9:6f:d7:36:58:e9:50:87:c3:71:
                    10:34:a1:8f:22:aa:fe:ed:78:bc:f5:03:27:76:64:
                    2b:b4:45:29:31:90:12:cb:ce:df:12:ce:42:a2:64:
                    76:18:93:9e:4b:da:e4:b1:8b:f5:e8:d8:96:ad:20:
                    28:2d:4e:f7:fb:06:7a:7f:58:6d:d6:f4:80:81:e5:
                    94:aa:1e:ed:7a:46:ab:9c:0f:bd:5b:61:7c:7d:e9:
                    cd:65
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Key Identifier: 
                B3:84:EB:76:FE:A0:68:9E:37:D5:83:5E:40:9D:5D:71:1E:C5:B7:43
            X509v3 Authority Key Identifier: 
                keyid:B3:84:EB:76:FE:A0:68:9E:37:D5:83:5E:40:9D:5D:71:1E:C5:B7:43

            X509v3 Basic Constraints: critical
                CA:TRUE
    Signature Algorithm: sha256WithRSAEncryption
         7e:e7:14:5c:77:1e:67:f4:72:84:df:e3:8e:f2:7e:5b:45:f2:
         0e:e7:5e:0d:2d:92:60:85:6c:b1:95:73:9f:71:c8:ce:98:e0:
         db:86:e8:7c:a6:41:05:82:15:cb:82:dd:fd:ae:f1:65:7c:9c:
         b4:6a:d4:48:af:87:f8:23:13:ab:3a:1f:d6:14:16:0c:05:d1:
         8f:c5:3f:75:ae:96:36:08:09:7c:52:51:d1:14:97:9c:27:0b:
         dd:3a:2d:37:82:d1:2e:c0:e1:84:c6:be:c7:f5:60:49:8a:c2:
         85:1f:87:41:ff:34:14:8a:89:07:1a:70:81:0b:70:55:d0:9b:
         91:6a:85:2d:9e:f1:b3:37:db:e4:80:7d:bf:41:b7:aa:33:5c:
         d8:cc:45:a9:50:f9:56:e4:15:fd:20:3b:4a:1c:ca:44:d7:d6:
         f0:fc:de:d3:76:35:ac:79:03:a1:f0:39:8e:10:fa:00:d2:10:
         6c:c8:19:6d:13:0d:e2:08:0f:f0:3d:b6:e8:09:c8:a6:6b:02:
         a9:e3:d7:14:10:8e:f6:9f:cb:54:cb:47:ad:db:5f:95:93:38:
         84:73:24:c1:0e:9f:53:96:25:95:01:c7:8c:1e:dd:5b:99:40:
         4f:8f:89:b5:ee:c2:3e:03:ed:5e:30:85:ba:98:8d:30:f7:da:
         44:23:9d:e5

```

Step 3: Generate Certificate Signing request

```
root@sdi04a:/CertificateAuthority# openssl genrsa -out tls.key 2048
Generating RSA private key, 2048 bit long modulus (2 primes)
.+++++
...........................+++++
e is 65537 (0x010001)
```

Step 4: Use the key to create a Signing Request

```
root@sdi04a:/CertificateAuthority# openssl req -new -key tls.key -out tls.csr
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:DE
State or Province Name (full name) [Some-State]:Baden-Württemberg
Locality Name (eg, city) []:Stuttgart
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Hochschule der Medien
Organizational Unit Name (eg, section) []:SDI_04
Common Name (e.g. server FQDN or YOUR name) []:Die_Seeligen
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:ASDF
An optional company name []:Gruppe_04 
```

Step 5: Next, list the domains that you want to certify in the `openssl.cnf`

```
# Extensions to add to a certificate request
basicConstraints       = CA:FALSE
authorityKeyIdentifier = keyid:always, issuer:always
keyUsage               = nonRepudiation, digitalSignature, keyEncipherment, dataEncipherment
subjectAltName         = @alt_names

[alt_names]
DNS.1 = *.g4.sdi.mi.hdm-stuttgart.de
```

Step 6: Sign the CSR by executing this command:

```
root@sdi04a:~/CertificateAuthority# openssl x509 -req     -in tls.csr     -CA rootCA.pem     -CAkey rootCA.key     -CAcreateserial     -out tls.crt     -days 730     -sha256     -extfile openssl.cnf
Signature ok
subject=C = DE, ST = Baden-W\C3\83\C2\BCrttemberg, L = Stuttgart, O = Hochschule der Medien, OU = SDI_04, CN = Die_Seeligen
Getting CA Private Key
Enter pass phrase for rootCA.key: PASS_PHRASE
```

Step 7: Next, you should verify the functionality of your CA:

```
root@sdi04a:~/CertificateAuthority# openssl verify -CAfile rootCA.pem -verify_hostname doc4.g4.sdi.mi.hdm-stuttgart.de tls.crt
tls.crt: OK
```

Step 8: Copy the Certificate Authority into the key

```
cp /root/CertificateAuthority/tls.crt /etc/ssl/certs/ssl-cert-g4.crt
cp /root/CertificateAuthority/tls.key /etc/ssl/private/ssl-cert-g4.key
```

### Configuring certificate in web server
Add the following lines the `g4-doc.conf` in the `/etc/apache2/sites-available/` directory.

```
<VirtualHost *:443>
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/ssl-cert-g4.crt
    SSLCertificateKeyFile /etc/ssl/private/ssl-cert-g4.key
    
    # Pfad zu den Webinhalten
    DocumentRoot /var/www/sdidoc/_build/html
    ServerName mi.hdm-stuttgart.de
    ServerAlias doc4.g4.sdi.mi.hdm-stuttgart.de
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
Now test your configuration and reload apache.
```
$ apache2ctl configtest
$ service apache2 reload
```