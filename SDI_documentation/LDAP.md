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

2. Add the new attribute by clicking the new attribute button in the upper right corner:

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


## Extending an existing entry

To extend an existing object, we need to add an object to one of the object classes. To do this, right click on the objectClass and select 'New Value', select the posixAccount and press 'Add'.

```{image} ./images/posix_account.png
:alt: object editor 
:height: 500px
:align: center
```
After clicking Next, enter a value for gidNumber, homeDirectory and uidNumber. Finish the configuration by clicking Finish.

Now it should look like this:

```{image} ./images/posix_account_2.png
:alt: object editor 
:height: 500px
:align: center
```

## Filter based search
We can search the LDAP using filters by clicking on "Search" and then on "New Search". Here are some examples:

### All users with a uid attribute value starting with the letter “b”.
The Filter for this is: 
```
(&
    (uid=b*)
    (objectClass=inetOrgPerson)
)
```

```{image} ./images/filter_01.png
:alt: Search editor
:height: 500px
:align: center
```

### All entries either with either a defined uid attribute or a ou attribute starting with letter “d”.
The Filter for this is: 

```
(|
    (uid=*)
    (ou=d*)
)
```

```{image} ./images/filter_02.png
:alt: Search editor
:height: 500px
:align: center
```

### All users entries within the whole DIT having a gidNumber value of 100.
The Filter for this is:
``` 
(&
    (gidNumber=100)
    (objectClass=inetOrgPerson)
)
```

```{image} ./images/filter_03.png
:alt: Search editor
:height: 500px
:align: center
```

### All user entries belonging to the billing department having a uid value greater than 1023.
The Filter for this is: 
```
(&
    (uidNumber>=1023)
    (objectClass=inetOrgPerson)
)
```
```{image} ./images/filter_04.png
:alt: Search editor
:height: 500px
:align: center
```

### All user entries within the whole DIT having a commonName containing the substring “ei”.
The Filter for this is: 
```
(&
    (objectClass=inetOrgPerson)
    (cn=*El*)
)
```
```{image} ./images/filter_05.png
:alt: Search editor
:height: 500px
:align: center
```

### All user entries within the whole DIT belonging to gidNumber == 100 or having a uid value starting with letter “t”.
The Filter for this is: 
``` 
(&
    (objectClass=inetOrgPerson)
    (gidNumber=100)
    (uid=t*)
)
```
```{image} ./images/filter_06.png
:alt: Search editor
:height: 500px
:align: center
```

## Accessing LDAP data by a mail client

To use LDAP data with a mail client, it is necessary to add a new address book. It's configuration is shown in the image below:

```{image} ./images/mail_ldap.png
:alt: mail client ldap setup
:height: 500px
:align: center
```

After configuring the address book, addresses are only available when searching. As shown in the image below, we can now find the characters we created when populating our tree.

```{image} ./images/ldap_jim.png
:alt: address book
:width: 100%
:align: center
```
## LDAP configuration

### Update the config

1. Create the file add_olcRootPW.ldif  with `touch add_olcRootPW.ldif`.

2. Edit the add_oldRootPW.ldif file by incorporating a securely hashed password, ensuring its format aligns with the provided sample.
```
# cat add_olcRootPW.ldif 
dn: olcDatabase={0}config,cn=config
replace: olcRootPW
olcRootPW: {SSHA}KnvDQNEMVSLY83zQmSnVXwDOY8kKyc+K
```

3. Now we set the new configuration for LDAP as follows:
```
root@sdi04a:~/ldap_config# ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f add_olcRootPW.ldif
modifying entry "olcDatabase={0}config,cn=config"
```

4. You can now get the ldap configuration with this command: 
```
root@sdi04a:~/ldap_config# ldapsearch -Y EXTERNAL -H ldapi:/// -b cn=config
```
```
root@sdi04a:~/ldap_config# ldapsearch -Y EXTERNAL -H ldapi:/// -b cn=config
SASL/EXTERNAL authentication started
SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth
SASL SSF: 0
# extended LDIF
#
# LDAPv3
# base <cn=config> with scope subtree
# filter: (objectclass=*)
# requesting: ALL
#

# config
dn: cn=config
objectClass: olcGlobal
cn: config
olcArgsFile: /var/run/slapd/slapd.args
olcLogLevel: none
olcPidFile: /var/run/slapd/slapd.pid
olcToolThreads: 1

# module{0}, config
dn: cn=module{0},cn=config
objectClass: olcModuleList
cn: module{0}
olcModulePath: /usr/lib/ldap
olcModuleLoad: {0}back_mdb

# schema, config
dn: cn=schema,cn=config
objectClass: olcSchemaConfig
cn: schema
olcObjectIdentifier: OLcfg 1.3.6.1.4.1.4203.1.12.2
olcObjectIdentifier: OLcfgAt OLcfg:3
olcObjectIdentifier: OLcfgGlAt OLcfgAt:0
olcObjectIdentifier: OLcfgBkAt OLcfgAt:1
olcObjectIdentifier: OLcfgDbAt OLcfgAt:2
...
olcDatabase: {-1}frontend
olcAccess: {0}to * by dn.exact=gidNumber=0+uidNumber=0,cn=peercred,cn=external
 ,cn=auth manage by * break
olcAccess: {1}to dn.exact="" by * read
olcAccess: {2}to dn.base="cn=Subschema" by * read
olcSizeLimit: 500

# {0}config, config
dn: olcDatabase={0}config,cn=config
objectClass: olcDatabaseConfig
olcDatabase: {0}config
olcAccess: {0}to * by dn.exact=gidNumber=0+uidNumber=0,cn=peercred,cn=external
 ,cn=auth manage by * break
olcRootDN: cn=admin,cn=config
olcRootPW: {SSHA}KnvDQNEMVSLY83zQmSnVXwDOY8kKyc+K

# {1}mdb, config
dn: olcDatabase={1}mdb,cn=config
objectClass: olcDatabaseConfig
objectClass: olcMdbConfig
olcDatabase: {1}mdb
olcDbDirectory: /var/lib/ldap
olcSuffix: dc=betrayer,dc=com
olcAccess: {0}to attrs=userPassword by self write by anonymous auth by * none
olcAccess: {1}to attrs=shadowLastChange by self write by * read
olcAccess: {2}to * by * read
olcLastMod: TRUE
olcRootDN: cn=admin,dc=betrayer,dc=com
olcRootPW: {SSHA}nNncI348rYAJSDSM68q0GDe5sK5rxhZR
olcDbCheckpoint: 512 30
olcDbIndex: objectClass eq
olcDbIndex: cn,uid eq
olcDbIndex: uidNumber,gidNumber eq
olcDbIndex: member,memberUid eq
olcDbMaxSize: 1073741824

# search result
search: 2
result: 0 Success

# numResponses: 11
# numEntries: 10
```

### Connecting the LDAP

1. Create a new connection .
```{image} ./images/config_01.png
:alt: Ldap configuration
:height: 500px
:align: center
```

2. Authentify with the config account.

```{image} ./images/config_02.png
:alt: Ldap configuration
:height: 500px
:align: center
```

3. Set the Base DN to `cn=config`.


```{image} ./images/config_03.png
:alt: Ldap configuration
:height: 500px
:align: center
```

4. After connecting you can change the `olcLogLevel`. You can get the most information by using a `olcLogLevel` of -1.
```{image} ./images/config_04.png
:alt: Change loging level
:width: 100%
:align: center
```

5. Open the Log using this command: `cat /var/log/syslog`
```
Jul 25 11:44:05 sdi04a slapd[165]: => access_allowed: result not in cache (olcToolThreads)
Jul 25 11:44:05 sdi04a slapd[165]: => access_allowed: read access to "cn=config" "olcToolThreads" requested
Jul 25 11:44:05 sdi04a slapd[165]: <= root access granted
Jul 25 11:44:05 sdi04a slapd[165]: => access_allowed: read access granted by manage(=mwrscxd)
Jul 25 11:44:05 sdi04a slapd[165]: => access_allowed: result not in cache (olcLogLevel)
Jul 25 11:44:05 sdi04a slapd[165]: => access_allowed: read access to "cn=config" "olcLogLevel" requested
Jul 25 11:44:05 sdi04a slapd[165]: <= root access granted
Jul 25 11:44:05 sdi04a slapd[165]: => access_allowed: read access granted by manage(=mwrscxd)
Jul 25 11:44:05 sdi04a slapd[165]: conn=1011 op=11 ENTRY dn="cn=config"
Jul 25 11:44:05 sdi04a slapd[165]: <= send_search_entry: conn 1011 exit.
Jul 25 11:44:05 sdi04a slapd[165]: send_ldap_result: conn=1011 op=11 p=3
Jul 25 11:44:05 sdi04a slapd[165]: send_ldap_result: err=0 matched="" text=""
Jul 25 11:44:05 sdi04a slapd[165]: send_ldap_response: msgid=12 tag=101 err=0
Jul 25 11:44:05 sdi04a slapd[165]: conn=1011 op=11 SEARCH RESULT tag=101 err=0 nentries=1 text=
Jul 25 11:44:05 sdi04a slapd[165]: daemon: activity on 1 descriptor
Jul 25 11:44:05 sdi04a slapd[165]: daemon: activity on:
Jul 25 11:44:05 sdi04a slapd[165]: 
Jul 25 11:44:05 sdi04a slapd[165]: daemon: epoll: listen=8 active_threads=0 tvp=zero
Jul 25 11:44:05 sdi04a slapd[165]: daemon: epoll: listen=9 active_threads=0 tvp=zero
Jul 25 11:44:05 sdi04a slapd[165]: daemon: epoll: listen=10 active_threads=0 tvp=zero
Jul 25 11:51:56 sdi04a slapd[165]: daemon: epoll: listen=8 active_threads=0 tvp=zero
Jul 25 11:51:56 sdi04a slapd[165]: daemon: epoll: listen=9 active_threads=0 tvp=zero
Jul 25 11:51:56 sdi04a slapd[165]: daemon: epoll: listen=10 active_threads=0 tvp=zero
Jul 25 11:53:58 sdi04a systemd[1]: Started OpenBSD Secure Shell server per-connection daemon (10.100.16.26:54456).
Jul 25 11:53:59 sdi04a systemd[1]: Started Session 914967 of user root.
(END)
```



## LDAP based user login

1. Create a backup of our working PAM configuration using this commands:
```
cd /etc
tar zcf /root/pam.tgz pam.conf pam.d
```

Use `tar ztf /root/pam.tgz ` to see if it worked it should look like this:

```
# tar ztf /root/pam.tgz 
pam.conf
pam.d/
pam.d/sudo
pam.d/chsh
pam.d/runuser
pam.d/chfn
pam.d/common-auth
pam.d/other
pam.d/su
pam.d/su-l
pam.d/common-session-noninteractive
pam.d/passwd
pam.d/newusers
pam.d/cron
pam.d/common-password
pam.d/common-session
pam.d/common-account
pam.d/login
pam.d/sshd
pam.d/chpasswd
pam.d/runuser-l
```

2. Install libpam-ldapd with `apt install libpam-ldapd`.

3. Enter your LDAP URI.

```

 ┌──────────────────────────────────────┤ Konfiguriere nslcd ├───────────────────────────────────────┐
 │ Bitte geben Sie den Uniform Resource Identifier des benutzten LDAP-Servers ein. Das Format ist    │ 
 │ »ldap://<Rechnername oder IP-Adresse>:<Port>/«. Alternativ kann auch »ldaps://« oder »ldapi://«   │ 
 │ benutzt werden. Der Port muss nicht angegeben werden.                                             │ 
 │                                                                                                   │ 
 │ Wenn Sie »ldap« oder »ldaps« verwenden, sollten Sie eine IP-Adresse eingeben, um Ausfälle zu      │ 
 │ verhindern, falls die Namensauflösung einmal nicht verfügbar ist.                                 │ 
 │                                                                                                   │ 
 │ Mehrere URIs können, durch Leerzeichen getrennt, angegeben werden.                                │ 
 │                                                                                                   │ 
 │ URI des LDAP-Servers:                                                                             │ 
 │                                                                                                   │ 
 │ ldap://sdi04a.mi.hdm-stuttgart.de________________________________________________________________ │ 
 │                                                                                                   │ 
 │                            <Ok>                                <Abbrechen>                        │ 
 │                                                                                                   │ 
 └───────────────────────────────────────────────────────────────────────────────────────────────────┘ 
                                                                                                       
```

4. Enter your LDAP searchbase.
```

 ┌──────────────────────────────────────┤ Konfiguriere nslcd ├───────────────────────────────────────┐
 │ Bitte geben Sie den DN (distinguished name) der LDAP-Suchbasis ein. Oft werden Teile des          │ 
 │ Domänennamens für diesen Zweck benutzt. Beispielsweise würde bei der Domäne »example.net« der DN  │ 
 │ »dc=example,dc=net« als Suchbasis verwendet werden.                                               │ 
 │                                                                                                   │ 
 │ Suchbasis des LDAP-Servers:                                                                       │ 
 │                                                                                                   │ 
 │ dc=betrayer, dc=com______________________________________________________________________________ │ 
 │                                                                                                   │ 
 │                            <Ok>                                <Abbrechen>                        │ 
 │                                                                                                   │ 
 └───────────────────────────────────────────────────────────────────────────────────────────────────┘ 
                                                                                                       
```
4. Normally a window would open now to configure the package, but we made a mistake by hitting enter and thus closed the configuration wizard, so to configure the last step we had to do it manually by opening nsswitch.conf and editing it accordingly:

```
# /etc/nsswitch.conf
#
# Example configuration of GNU Name Service Switch functionality.
# If you have the `glibc-doc-reference' and `info' packages installed, try:
# `info libc "Name Service Switch"' for information about this file.

passwd:         files ldap   
group:          files ldap   
shadow:         files ldap
gshadow:        files

hosts:          files dns
networks:       files

protocols:      db files
services:       db files
ethers:         db files
rpc:            db files

netgroup:       nis
```
5. Afterwards, reboot with `systemctl restart nslcd` to make sure the new config is used, and test it with a test user's id. Make sure your test user's id is above 1000, otherwise you will not be able to use it to log in to the vm:

```
# id rainer
uid=1002(rainer) gid=1002 Gruppen=1002
```

6. `PasswordAuthentication` should be set to `yes` in `/etc/ssh/sshd_config`:

```
# cat /etc/ssh/sshd_config

#	$OpenBSD: sshd_config,v 1.103 2018/04/09 20:41:22 tj Exp $

# This is the sshd server system-wide configuration file.  See
# sshd_config(5) for more information.

# This sshd was compiled with PATH=/usr/bin:/bin:/usr/sbin:/sbin

# The strategy used for options in the default sshd_config shipped with
# OpenSSH is to specify options with their default value where
# possible, but leave them commented.  Uncommented options override the
# default value.

Include /etc/ssh/sshd_config.d/*.conf

#Port 22
#AddressFamily any
#ListenAddress 0.0.0.0
#ListenAddress ::

#HostKey /etc/ssh/ssh_host_rsa_key
#HostKey /etc/ssh/ssh_host_ecdsa_key
#HostKey /etc/ssh/ssh_host_ed25519_key

# Ciphers and keying
#RekeyLimit default none

# Logging
#SyslogFacility AUTH
#LogLevel INFO

# Authentication:

#LoginGraceTime 2m
#PermitRootLogin prohibit-password
#StrictModes yes
#MaxAuthTries 6
#MaxSessions 10

#PubkeyAuthentication yes

# Expect .ssh/authorized_keys2 to be disregarded by default in future.
#AuthorizedKeysFile	.ssh/authorized_keys .ssh/authorized_keys2

#AuthorizedPrincipalsFile none

#AuthorizedKeysCommand none
#AuthorizedKeysCommandUser nobody

# For this to work you will also need host keys in /etc/ssh/ssh_known_hosts
#HostbasedAuthentication no
# Change to yes if you don't trust ~/.ssh/known_hosts for
# HostbasedAuthentication
#IgnoreUserKnownHosts no
# Don't read the user's ~/.rhosts and ~/.shosts files
#IgnoreRhosts yes

# To disable tunneled clear text passwords, change to no here!
PasswordAuthentication yes
#PermitEmptyPasswords no

# Change to yes to enable challenge-response passwords (beware issues with
# some PAM modules and threads)
ChallengeResponseAuthentication no

# Kerberos options
#KerberosAuthentication no
#KerberosOrLocalPasswd yes
#KerberosTicketCleanup yes
#KerberosGetAFSToken no

# GSSAPI options
#GSSAPIAuthentication no
#GSSAPICleanupCredentials yes
#GSSAPIStrictAcceptorCheck yes
#GSSAPIKeyExchange no

# Set this to 'yes' to enable PAM authentication, account processing,
# and session processing. If this is enabled, PAM authentication will
# be allowed through the ChallengeResponseAuthentication and
# PasswordAuthentication.  Depending on your PAM configuration,
# PAM authentication via ChallengeResponseAuthentication may bypass
# the setting of "PermitRootLogin without-password".
# If you just want the PAM account and session checks to run without
# PAM authentication, then enable this but set PasswordAuthentication
# and ChallengeResponseAuthentication to 'no'.
UsePAM yes

#AllowAgentForwarding yes
#AllowTcpForwarding yes
#GatewayPorts no
X11Forwarding yes
#X11DisplayOffset 10
#X11UseLocalhost yes
#PermitTTY yes
PrintMotd no
#PrintLastLog yes
#TCPKeepAlive yes
#PermitUserEnvironment no
#Compression delayed
#ClientAliveInterval 0
#ClientAliveCountMax 3
#UseDNS no
#PidFile /var/run/sshd.pid
#MaxStartups 10:30:100
#PermitTunnel no
#ChrootDirectory none
#VersionAddendum none

# no default banner path
#Banner none

# Allow client to pass locale environment variables
AcceptEnv LANG LC_*

# override default of no subsystems
Subsystem	sftp	/usr/lib/openssh/sftp-server

# Example of overriding settings on a per-user basis
#Match User anoncvs
#	X11Forwarding no
#	AllowTcpForwarding no
#	PermitTTY no
#	ForceCommand cvs server
```
7. Make sure the results are not influenced by unwanted caching, by stoping the nscd service.
```
# systemctl stop nscd
# systemctl status nscd
● nscd.service - Name Service Cache Daemon
     Loaded: loaded (/lib/systemd/system/nscd.service; enabled; vendor preset: enabled)
     Active: inactive (dead) since Tue 2023-07-25 14:22:15 CEST; 6s ago
    Process: 40931 ExecStart=/usr/sbin/nscd (code=exited, status=0/SUCCESS)
    Process: 41017 ExecStop=/usr/sbin/nscd --shutdown (code=exited, status=0/SUCCESS)
   Main PID: 40932 (code=exited, status=0/SUCCESS)
        CPU: 24ms

Jul 25 14:01:41 sdi04b nscd[40932]: 40932 überwache das Verzeichnis »/etc« (2)
Jul 25 14:01:41 sdi04b nscd[40932]: 40932 Die inotify-basierte Überwachung für die Datei »/etc/netgroup>
Jul 25 14:01:41 sdi04b nscd[40932]: 40932 »stat« für die Datei »/etc/netgroup« ist fehlgeschlagen; weit>
Jul 25 14:01:41 sdi04b systemd[1]: Started Name Service Cache Daemon.
Jul 25 14:02:00 sdi04b nscd[40932]: 40932 Prüfe auf überwachte Datei »/etc/netgroup«: Datei oder Verzei>
Jul 25 14:05:59 sdi04b nscd[40932]: 40932 überwache Datei »/etc/resolv.conf« (5)
Jul 25 14:05:59 sdi04b nscd[40932]: 40932 überwache das Verzeichnis »/etc« (2)
Jul 25 14:22:15 sdi04b systemd[1]: Stopping Name Service Cache Daemon...
Jul 25 14:22:15 sdi04b systemd[1]: nscd.service: Succeeded.
Jul 25 14:22:15 sdi04b systemd[1]: Stopped Name Service Cache Daemon.
```
8. Now create a new home directory and change the access permissions. You can do this with this command:`mkhomedir_helper rainer`

9: To test the login use this command:
```
ssh rainer@sdi04b.mi.hdm-stuttgart.de 
rainer@sdi04b.mi.hdm-stuttgart.de's password: YOUR_PASSWORD
```
10. You can test if the user has the home directory by using this command: `ls -la`.
```
drwxr-xr-x  2 rainer   1002  5 25. Jul 14:15 rainer
```

## Backup and recovery / restore

To make a backup and save it on another VM, you need to make a backup of your ldap database in the first vm and paste it into the ldap in the second VM.
If your second VM does not have an LDAP server, create one.

1. You can create a backup of the original ldap like this:
```
# slapcat -b cn=config -l ldap-config.ldif 
# slapcat -l ldap-data.ldif
```

2. To copy the backup to the other vm, you can use ssh to transmit it. Generate a public ssh key in the first vm, copy it and paste it into the "authorized_keys"-file of the second vm.

3. Establish an connection via "scp" and transmit the "config" and "data" file of the ldap backup. These are the commands to do it:
```
# scp /root/ldap_config/ldap-config.ldif root@sdi04b.mi.hdm-stuttgart.de:/root/ldap_config/ldap-config.ldif
ldap-config.ldif                                                      100%   38KB  34.5MB/s   00:00    
```
```
# scp /root/ldap_config/ldap-data.ldif  root@sdi04b.mi.hdm-stuttgart.de:/root/ldap_config/ldap-data.ldif
ldap-data.ldif                                                        100% 3793     6.1MB/s   00:00 
```
4. Login into your other vm and install the backups by calling these commands:
```
# slapadd -b cn=config -l ~/ldap_config/ldap-config.ldif -F /etc/ldap/slapd.d/
_#################### 100.00% eta   none elapsed            none fast!         
Closing DB...
# slapadd -n 1 -l ~/ldap_config/ldap-data.ldif -F /etc/ldap/slapd.d/
_#################### 100.00% eta   none elapsed            none fast!         
Closing DB...
```

5. Next, we use the "slapcat" command to extract data from the LDAP directory and store it in a file called "ldap-data-b.ldif" within the "~/ldap_config" directory. This process effectively transfers the data from the LDAP directory to the specified LDIF file.

```
# slapcat -l ldap-data-b.ldif
```
6. To verify the successful transfer of data, we can then examine the contents of the newly created LDIF file and verify that it is the same as the backup LDIF.
```
# cat ldap-data-b.ldif 
dn: dc=betrayer,dc=com
objectClass: top
objectClass: dcObject
objectClass: organization
o: gruppe_04
dc: betrayer
structuralObjectClass: organization
entryUUID: f0877648-8772-103d-9927-f5e99155b8e2
creatorsName: cn=admin,dc=betrayer,dc=com
createTimestamp: 20230515134838Z
entryCSN: 20230515134838.577967Z#000000#000#000000
modifiersName: cn=admin,dc=betrayer,dc=com
modifyTimestamp: 20230515134838Z

dn: ou=departments,dc=betrayer,dc=com
ou: departments
objectClass: organizationalUnit
objectClass: top
structuralObjectClass: organizationalUnit
entryUUID: 03925dae-877b-103d-80ae-693c87ddfaaf
creatorsName: cn=admin,dc=betrayer,dc=com
createTimestamp: 20230515144626Z
entryCSN: 20230515144626.500097Z#000000#000#000000
modifiersName: cn=admin,dc=betrayer,dc=com
modifyTimestamp: 20230515144626Z

dn: ou=software,ou=departments,dc=betrayer,dc=com
ou: software
objectClass: top
objectClass: organizationalUnit
structuralObjectClass: organizationalUnit
entryUUID: f051f770-8cf4-103d-80af-693c87ddfaaf
creatorsName: cn=admin,dc=betrayer,dc=com
createTimestamp: 20230522140148Z
entryCSN: 20230522140148.616237Z#000000#000#000000
modifiersName: cn=admin,dc=betrayer,dc=com
modifyTimestamp: 20230522140148Z

dn: ou=financial,ou=departments,dc=betrayer,dc=com
ou: financial
objectClass: top
objectClass: organizationalUnit
structuralObjectClass: organizationalUnit
entryUUID: f054725c-8cf4-103d-80b0-693c87ddfaaf
creatorsName: cn=admin,dc=betrayer,dc=com
createTimestamp: 20230522140148Z
entryCSN: 20230522140148.632495Z#000000#000#000000
modifiersName: cn=admin,dc=betrayer,dc=com
modifyTimestamp: 20230522140148Z

dn: ou=devel,ou=software,ou=departments,dc=betrayer,dc=com
ou: devel
objectClass: top
objectClass: organizationalUnit
structuralObjectClass: organizationalUnit
entryUUID: f056011c-8cf4-103d-80b1-693c87ddfaaf
creatorsName: cn=admin,dc=betrayer,dc=com
createTimestamp: 20230522140148Z
entryCSN: 20230522140148.642704Z#000000#000#000000
modifiersName: cn=admin,dc=betrayer,dc=com
modifyTimestamp: 20230522140148Z

dn: ou=testing,ou=software,ou=departments,dc=betrayer,dc=com
ou: testing
objectClass: top
objectClass: organizationalUnit
structuralObjectClass: organizationalUnit
entryUUID: f0577ccc-8cf4-103d-80b2-693c87ddfaaf
creatorsName: cn=admin,dc=betrayer,dc=com
createTimestamp: 20230522140148Z
entryCSN: 20230522140148.652424Z#000000#000#000000
modifiersName: cn=admin,dc=betrayer,dc=com
modifyTimestamp: 20230522140148Z

dn: uid=bean,ou=devel,ou=software,ou=departments,dc=betrayer,dc=com
uid: bean
mail: bean@betrayer.com
givenName: Jim
cn: Jim Bean
sn: Bean
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
objectClass: posixAccount
structuralObjectClass: inetOrgPerson
entryUUID: f059010a-8cf4-103d-80b3-693c87ddfaaf
creatorsName: cn=admin,dc=betrayer,dc=com
createTimestamp: 20230522140148Z
homeDirectory: /home/jim
gidNumber: 1
userPassword:: e1NTSEF9enlPV20zTCt1cmZKa2pBNlArTjM2WVlTMVdhZG5OZzc3Q2ExcVE9P
 Q==
uidNumber: 1001
entryCSN: 20230725120124.645863Z#000000#000#000000
modifiersName: cn=admin,dc=betrayer,dc=com
modifyTimestamp: 20230725120124Z

dn: cn=Rainer Zufall,ou=devel,ou=software,ou=departments,dc=betrayer,dc=com
uid: rainer
uidNumber: 1002
homeDirectory: /home/rainer
gidNumber: 1002
sn: Rainer
objectClass: inetOrgPerson
objectClass: organizationalPerson
objectClass: person
objectClass: top
objectClass: posixAccount
structuralObjectClass: inetOrgPerson
entryUUID: 5bf37568-bf30-103d-9479-47ebfcdeb317
creatorsName: cn=admin,dc=betrayer,dc=com
createTimestamp: 20230725121307Z
cn: Rainer Zufall
userPassword:: e1NTSEF9Zm9BL1phZUhCUy9haDBibGdjcDZCNUlta0dLOE9Ecy9KcmpuMGc9P
 Q==
entryCSN: 20230725121504.037127Z#000000#000#000000
modifiersName: cn=admin,dc=betrayer,dc=com
modifyTimestamp: 20230725121504Z
```

## Accessing LDAP by a Java™ application.

The following code example shows how to connect to the LDAP server:

```
import javax.naming.*;
import javax.naming.directory.*;
import java.util.Hashtable;

public class Main {
    public static void main(String[] args) {

        String ldapUrl = "ldap://141.62.75.104:389"; // Adjust the URL and port as per your LDAP server configuration
        String username = "cn=admin,dc=betrayer,dc=com"; // Replace with your admin user DN
        String password = "qwertz"; // Replace with your admin user password
        Hashtable<String, String> env = new Hashtable<>();
        env.put(Context.INITIAL_CONTEXT_FACTORY, "com.sun.jndi.ldap.LdapCtxFactory");
        env.put(Context.PROVIDER_URL, ldapUrl);
        env.put(Context.SECURITY_AUTHENTICATION, "simple");
        env.put(Context.SECURITY_PRINCIPAL, username);
        env.put(Context.SECURITY_CREDENTIALS, password);


        DirContext ctx = null;
        try {
            ctx = new InitialDirContext(env);
            System.out.println("connected"); 
            // Connection successful, you can proceed with LDAP operations here
        } catch (NamingException e) {
            // Handle connection errors
            e.printStackTrace();
        } finally {
            System.out.println("connection Terminated");
            // Close the context when done
            if (ctx != null) {
                try {
                    ctx.close();
                } catch (NamingException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```
