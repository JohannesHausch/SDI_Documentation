# LDAP
## Browse an existing LDAP Server 

### Connect anonymously to an LDAP using Apache Directory Studio 
Connect anonymously to an LDAP server in our example we use: ldap1.hdm-stuttgart.de with TLS. (Depending on your location this may require VPN.)

```{image} ./images/LDAP_connection.png
:alt: Build LDAP connection
:height: 300px
:align: center
```

### Search anonymously through the DIT (Directory Information Tree)
Find your personal entry branch in the DIT by using a new Search: 
1. Set the search base, by either entering a specific node or ticking the box to search through all nodes.
2. Enter the search attributes, e.g. ```UID=sm256```

```{image} ./images/SearchByEntryAnonymously.png
:alt: search by entry anonymously
:height: 500px
:align: center
```



### Connect with simpleAuth to an LDAP using Apache Directory Studio and search through the DIT
In our example we use: ldap1.hdm-stuttgart.de with TLS. (Depending on your location this may require VPN.)

```{image} ./images/Simple_Auth.png
:alt: search by entry anonymously
:height: 300px
:align: center
```

Now you get more information than before:

```{image} ./images/SearchByEntryAuth.png
:alt: search by entry anonymously
:height: 500px
:align: center
```

### Connect anonymously to an LDAP using comand line and search anonymously through the DIT

To perform an LDAP search without the tool, use this 
command:
`$ ldapsearch -b "dc=hdm-stuttgart,dc=de" -H ldap://ldap1.hdm-stuttgart.de -x '(uid=sm256)'`

The result should look like this:
```
# extended LDIF
#
# LDAPv3
# base <dc=hdm-stuttgart,dc=de> with scope subtree
# filter: (uid=sm256)
# requesting: ALL
#

# sm256, userlist, hdm-stuttgart.de
dn: uid=sm256,ou=userlist,dc=hdm-stuttgart,dc=de
displayName: Maier Sebastian
employeeType: student
objectClass: hdmAccount
objectClass: hdmStudent
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
objectClass: eduPerson
eduPersonAffiliation: member
eduPersonAffiliation: student
eduPersonAffiliation: library-walk-in
uid: sm256
mail: sm256@hdm-Stuttgart.de
uidNumber: 71448
cn: Maier Sebastian
loginShell: /bin/sh
hdmCategory: 1
gidNumber: 100
givenName: Sebastian
homeDirectory: /home/stud/s/sm256
sn: Maier

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```
`$ ldapsearch -x -H ldap://ldap1.hdm-stuttgart.de -D "uid=sm256, ou=userlist,dc=hdm-stuttgart,dc=de" -W -b "dc=hdm-stuttgart,dc=de" '(uid=sm256)'`

```
Enter LDAP Password: 
# extended LDIF
#
# LDAPv3
# base <dc=hdm-stuttgart,dc=de> with scope subtree
# filter: (uid=sm256)
# requesting: ALL
#

# sm256, userlist, hdm-stuttgart.de
dn: uid=sm256,ou=userlist,dc=hdm-stuttgart,dc=de
businessCategory: 1
employeeType: student
postOfficeBox: 2G
objectClass: hdmAccount
objectClass: hdmStudent
objectClass: inetOrgPerson
objectClass: posixAccount
objectClass: shadowAccount
objectClass: eduPerson
eduPersonAffiliation: member
eduPersonAffiliation: student
eduPersonAffiliation: library-walk-in
uid: sm256
mail: sm256@hdm-Stuttgart.de
uidNumber: 71448
cn: Maier Sebastian
loginShell: /bin/sh
hdmCategory: 1
gidNumber: 100
employeeNumber: notYourBusiness
givenName: Sebastian
homeDirectory: /home/stud/s/sm256
sn: Maier
matrikelNr: notYourBuissnes
userPassword:: notYourBusiness
shadowLastChange: 18898
sambaNTPassword: notYourBusiness

# search result
search: 2
result: 0 Success

# numResponses: 2
# numEntries: 1
```

## Set up an OpenLdap server 

First you need to set up a root password: `$ passwd root`

Now we Install dialog to easily create GUI's `$ apt install dialog`

Then install slapd (the Stand-alone LDAP Daemon) using this command:`$ apt install slapd ldap-utils`


You will then be prompted for your root password, which you set earlier.

Based on the DNS domain mi.hdm-stuttgart.de the default slapd package installer configures a DIT having dc=mi,dc=hdm-stuttgart,dc=de as root by default. 

We do this by running this command: `$ dpkg-reconfigure slapd`.

This will open a GUI. Select the "Nein" option to ensure that an initial setup is created.:
```
              ┌───────────────────────────────────────┤ Konfiguriere slapd ├───────────────────────────────────────┐
              │                                                                                                    │ 
              │ Falls Sie diese Option aktivieren, wird keine Startkonfiguration oder Datenbank für Sie erstellt.  │ 
              │                                                                                                    │ 
              │ OpenLDAP-Server-Konfiguration auslassen?                                                           │ 
              │                                                                                                    │ 
              │                             <Ja>                                 <Nein>                            │ 
              │                                                                                                    │ 
              └────────────────────────────────────────────────────────────────────────────────────────────────────┘ 

```
Next, you need to Change the DNS-Domainname to betrayer. Type in ```betryer.com``` and select "OK".
```

        ┌─────────────────────────────────────────────┤ Konfiguriere slapd ├──────────────────────────────────────────────┐
        │ Der DNS-Domainname wird zur Erzeugung des Basis-DN Ihres LDAP-Verzeichnisses verwendet. Zum Beispiel erstellt   │ 
        │ »foo.example.org« das Verzeichnis mit der Basis-DN »dc=foo, dc=example, dc=org«.                                │ 
        │                                                                                                                 │ 
        │ DNS-Domainname:                                                                                                 │ 
        │                                                                                                                 │ 
        │ betrayer.com___________________________________________________________________________________________________ │ 
        │                                                                                                                 │ 
        │                                                     <Ok>                                                        │ 
        │                                                                                                                 │ 
        └─────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘ 
                                                                                                                            

```
Put in your root Password 2 Times and select "Ok"
```

                  ┌───────────────────────────────────┤ Konfiguriere slapd ├───────────────────────────────────┐
                  │ Bitte geben Sie das Passwort für den Administrator-Eintrag in Ihrem LDAP-Verzeichnis ein.  │ 
                  │                                                                                            │ 
                  │ Administrator-Passwort:                                                                    │ 
                  │                                                                                            │ 
                  │ ******____________________________________________________________________________________ │ 
                  │                                                                                            │ 
                  │                                           <Ok>                                             │ 
                  │                                                                                            │ 
                  └────────────────────────────────────────────────────────────────────────────────────────────┘ 
                                                                                                                 

```

```

 ┌────────────────────────────────────────────────────┤ Konfiguriere slapd ├────────────────────────────────────────────────────┐
 │ Bitte geben Sie das Passwort für den Administrator-Eintrag Ihres LDAP-Verzeichnisses nochmal ein, um sicher zu gehen, dass   │ 
 │ Sie es richtig eingegeben haben.                                                                                             │ 
 │                                                                                                                              │ 
 │ Passwort bestätigen:                                                                                                         │ 
 │                                                                                                                              │ 
 │ ******______________________________________________________________________________________________________________________ │ 
 │                                                                                                                              │ 
 │                                                            <Ok>                                                              │ 
 │                                                                                                                              │ 
 └──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘ 
                                                                                                                                  

```
We want that the Databse deletes everything if we delete slapd so we chose now "Ja"
```

                          ┌───────────────────────────┤ Konfiguriere slapd ├───────────────────────────┐
                          │                                                                            │ 
                          │                                                                            │ 
                          │                                                                            │ 
                          │ Soll die Datenbank entfernt werden, wenn slapd vollständig gelöscht wird?  │ 
                          │                                                                            │ 
                          │                     <Ja>                         <Nein>                    │ 
                          │                                                                            │ 
                          └────────────────────────────────────────────────────────────────────────────┘ 
                                                                                                         
```
If there are older files, they could mess up the configuration process, so we have selected "Ja" to have the old database moved.
```

  ┌───────────────────────────────────────────────────┤ Konfiguriere slapd ├────────────────────────────────────────────────────┐
  │                                                                                                                             │ 
  │ Es sind noch Dateien in /var/lib/ldap, die wahrscheinlich den Konfigurationsprozess durcheinander bringen werden. Wird      │ 
  │ diese Option aktiviert, dann werden die Betreuerskripte die alten Datenbankdateien beiseite schieben, bevor sie eine neue   │ 
  │ Datenbank erstellen.                                                                                                        │ 
  │                                                                                                                             │ 
  │ Alte Datenbank verschieben?                                                                                                 │ 
  │                                                                                                                             │ 
  │                                     <Ja>                                         <Nein>                                     │ 
  │                                                                                                                             │ 
  └─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────┘ 
                                                                                                                                  

```


Since we will use a lot of LDAP utilty commands, it makes sense to set some defaults to save on typing time. For this reason we will set defaults for the Base and the URI. 
Access the config file via `$ nano /etc/ldap/ldap.conf` and add these lines:
```
BASE dc=betrayer,dc=com
URI ldap://ldap01.betrayer.com
```

## Populating the DIT

### Populate using ApacheDirectoryStudio
1. Use the GUI in the Apache client to create your first entry by right clicking and then selecting "Neuer Eintrag".
 
```{image} ./images/PopulateDIT_01.png
:alt: search by entry anonymously
:height: 500px
:align: center
```
2. Select your orginisational units (ou) and than continue.

```{image} ./images/PopulateDIT_02.png
:alt: search by entry anonymously
:height: 500px
:align: center
```
3. For the RDN enter `ou` and for the value `departments`, then continue.

```{image} ./images/PopulateDIT_03.png
:alt: search by entry anonymously
:height: 500px
:align: center
```
4. CLick "Fertigstellen"

```{image} ./images/PopulateDIT_04.png
:alt: search by entry anonymously
:height: 500px
:align: center
```

### Populate using LDIF-files

1. First you need to create a ldif file, be careful to use the correct extension (.ldif). You need to remove the entries from the ldif that you have already added.


It should look something like this:
```ldif
dn: ou=software,ou=departments,dc=betrayer,dc=com
objectClass: top
objectClass: organizationalUnit
ou: software

dn: ou=financial,ou=departments,dc=betrayer,dc=com
objectClass: top
objectClass: organizationalUnit
ou: financial

dn: ou=devel,ou=software,ou=departments,dc=betrayer,dc=com
objectClass: top
objectClass: organizationalUnit
ou: devel

dn: ou=testing,ou=software,ou=departments,dc=betrayer,dc=com
objectClass: top
objectClass: organizationalUnit
ou: testing

dn: uid=bean,ou=devel,ou=software,ou=departments,dc=betrayer,dc=com
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
cn: Jim Bean
sn: Bean
givenName: Jim
mail: bean@betrayer.com
uid: bean
userPassword:: e3NtZDV9YVhKL2JlVkF2TDRENk9pMFRLcDhjM3ovYTZQZzBXeHA=
```

2. Then you can use the ApacheDirectoryStudio import function.

```{image} ./images/import_ldif_01.png
:alt: Click on import
:height: 500px
:align: center
```

3. You need to select an import agent: LDIF in LDAP.

```{image} ./images/import_ldif_02.png
:alt: Select an import agent LDIF in LDAP
:height: 500px
:align: center
```

4. Next, browse your computer to select an ldif file to import and select which LDAP server to import it into.

```{image} ./images/import_ldif_03.png
:alt: import-options
:height: 500px
:align: center
```
5. Now it sould look like this:
```{image} ./images/import_ldif_04.png
:alt: Ldap-overview
:width: 100%
:align: center
```

## Testing a bind operation as non - admin user

### Add userPassword

1. You have already added a userPassword in the previous step. If you did not use the provided ldif file to add the userPassword, you must add the userPassword manually.

2. Add the new attribute by clicking the new attribute button in the upper right corner:3.

```{image} ./images/add_image_01.png
:alt: attribute button location
:width: 100%
:align: center
```

3. Choose "userPassword" as attribute type and finish.

```{image} ./images/add_image_02.png
:alt: attribute type selection
:height: 500px
:align: center
```

4. Double-click the value to open the password editor and set a new password.

```{image} ./images/add_image_03.png
:alt: password editor
:height: 500px
:align: center
```

### Create new connection

1. Create a new connection with the following authentication options:

```{image} ./images/bean_connection_01.png
:alt: password editor
:height: 500px
:align: center
```
2. The screen with the new connection should look like this:
```{image} ./images/bean_connection_02.png
:alt: password editor
:width: 100%
:align: center
```