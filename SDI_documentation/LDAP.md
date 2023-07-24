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
:height: 500px
:align: center
```

```
root@sdi04a:~# mkdir ldap_config
root@sdi04a:~# cd ldap_config/
root@sdi04a:~/ldap_config# touch add_olcRootPW.ldif
root@sdi04a:~/ldap_config# ll
insgesamt 10
drwxr-xr-x 2 root root  3 24. Jul 16:51 .
drwx------ 8 root root 17 24. Jul 16:51 ..
-rw-r--r-- 1 root root  0 24. Jul 16:51 add_olcRootPW.ldif
root@sdi04a:~/ldap_config# nano add_olcRootPW.ldif 
root@sdi04a:~/ldap_config# ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f ~/add_olcRootPW.ldif
/root/add_olcRootPW.ldif: No such file or directory
root@sdi04a:~/ldap_config# ldapmodify -Q -Y EXTERNAL -H ldapi:/// -f add_olcRootPW.ldif
modifying entry "olcDatabase={0}config,cn=config"

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
olcObjectIdentifier: OLcfgOvAt OLcfgAt:3
olcObjectIdentifier: OLcfgCtAt OLcfgAt:4
olcObjectIdentifier: OLcfgOc OLcfg:4
olcObjectIdentifier: OLcfgGlOc OLcfgOc:0
olcObjectIdentifier: OLcfgBkOc OLcfgOc:1
olcObjectIdentifier: OLcfgDbOc OLcfgOc:2
olcObjectIdentifier: OLcfgOvOc OLcfgOc:3
olcObjectIdentifier: OLcfgCtOc OLcfgOc:4
olcObjectIdentifier: OMsyn 1.3.6.1.4.1.1466.115.121.1
olcObjectIdentifier: OMsBoolean OMsyn:7
olcObjectIdentifier: OMsDN OMsyn:12
olcObjectIdentifier: OMsDirectoryString OMsyn:15
olcObjectIdentifier: OMsIA5String OMsyn:26
olcObjectIdentifier: OMsInteger OMsyn:27
olcObjectIdentifier: OMsOID OMsyn:38
olcObjectIdentifier: OMsOctetString OMsyn:40
olcAttributeTypes: ( 2.5.4.0 NAME 'objectClass' DESC 'RFC4512: object classes 
 of the entity' EQUALITY objectIdentifierMatch SYNTAX 1.3.6.1.4.1.1466.115.121
 .1.38 )
olcAttributeTypes: ( 2.5.21.9 NAME 'structuralObjectClass' DESC 'RFC4512: stru
 ctural object class of entry' EQUALITY objectIdentifierMatch SYNTAX 1.3.6.1.4
 .1.1466.115.121.1.38 SINGLE-VALUE NO-USER-MODIFICATION USAGE directoryOperati
 on )
olcAttributeTypes: ( 2.5.18.1 NAME 'createTimestamp' DESC 'RFC4512: time which
  object was created' EQUALITY generalizedTimeMatch ORDERING generalizedTimeOr
 deringMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.24 SINGLE-VALUE NO-USER-MODIFIC
 ATION USAGE directoryOperation )
olcAttributeTypes: ( 2.5.18.2 NAME 'modifyTimestamp' DESC 'RFC4512: time which
  object was last modified' EQUALITY generalizedTimeMatch ORDERING generalized
 TimeOrderingMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.24 SINGLE-VALUE NO-USER-M
 ODIFICATION USAGE directoryOperation )
olcAttributeTypes: ( 2.5.18.3 NAME 'creatorsName' DESC 'RFC4512: name of creat
 or' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.12 SING
 LE-VALUE NO-USER-MODIFICATION USAGE directoryOperation )
olcAttributeTypes: ( 2.5.18.4 NAME 'modifiersName' DESC 'RFC4512: name of last
  modifier' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.
 12 SINGLE-VALUE NO-USER-MODIFICATION USAGE directoryOperation )
olcAttributeTypes: ( 2.5.18.9 NAME 'hasSubordinates' DESC 'X.501: entry has ch
 ildren' EQUALITY booleanMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.7 SINGLE-VALU
 E NO-USER-MODIFICATION USAGE directoryOperation )
olcAttributeTypes: ( 2.5.18.10 NAME 'subschemaSubentry' DESC 'RFC4512: name of
  controlling subschema entry' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.
 4.1.1466.115.121.1.12 SINGLE-VALUE NO-USER-MODIFICATION USAGE directoryOperat
 ion )
olcAttributeTypes: ( 1.3.6.1.1.20 NAME 'entryDN' DESC 'DN of the entry' EQUALI
 TY distinguishedNameMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.12 SINGLE-VALUE N
 O-USER-MODIFICATION USAGE directoryOperation )
olcAttributeTypes: ( 1.3.6.1.1.16.4 NAME 'entryUUID' DESC 'UUID of the entry' 
 EQUALITY UUIDMatch ORDERING UUIDOrderingMatch SYNTAX 1.3.6.1.1.16.1 SINGLE-VA
 LUE NO-USER-MODIFICATION USAGE directoryOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.666.1.7 NAME 'entryCSN' DESC 'change seq
 uence number of the entry content' EQUALITY CSNMatch ORDERING CSNOrderingMatc
 h SYNTAX 1.3.6.1.4.1.4203.666.11.2.1{64} SINGLE-VALUE NO-USER-MODIFICATION US
 AGE directoryOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.666.1.13 NAME 'namingCSN' DESC 'change s
 equence number of the entry naming (RDN)' EQUALITY CSNMatch ORDERING CSNOrder
 ingMatch SYNTAX 1.3.6.1.4.1.4203.666.11.2.1{64} SINGLE-VALUE NO-USER-MODIFICA
 TION USAGE directoryOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.666.1.23 NAME 'syncreplCookie' DESC 'syn
 crepl Cookie for shadow copy' EQUALITY octetStringMatch ORDERING octetStringO
 rderingMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.40 SINGLE-VALUE NO-USER-MODIFI
 CATION USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.666.1.25 NAME 'contextCSN' DESC 'the lar
 gest committed CSN of a context' EQUALITY CSNMatch ORDERING CSNOrderingMatch 
 SYNTAX 1.3.6.1.4.1.4203.666.11.2.1{64} NO-USER-MODIFICATION USAGE dSAOperatio
 n )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.120.6 NAME 'altServer' DESC 'RFC4512
 : alternative servers' SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 USAGE dSAOperatio
 n )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.120.5 NAME 'namingContexts' DESC 'RF
 C4512: naming contexts' SYNTAX 1.3.6.1.4.1.1466.115.121.1.12 USAGE dSAOperati
 on )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.120.13 NAME 'supportedControl' DESC 
 'RFC4512: supported controls' SYNTAX 1.3.6.1.4.1.1466.115.121.1.38 USAGE dSAO
 peration )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.120.7 NAME 'supportedExtension' DESC
  'RFC4512: supported extended operations' SYNTAX 1.3.6.1.4.1.1466.115.121.1.3
 8 USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.120.15 NAME 'supportedLDAPVersion' D
 ESC 'RFC4512: supported LDAP versions' SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 U
 SAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.120.14 NAME 'supportedSASLMechanisms
 ' DESC 'RFC4512: supported SASL mechanisms' SYNTAX 1.3.6.1.4.1.1466.115.121.1
 .15 USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.1.3.5 NAME 'supportedFeatures' DESC 'RFC
 4512: features supported by the server' EQUALITY objectIdentifierMatch SYNTAX
  1.3.6.1.4.1.1466.115.121.1.38 USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.666.1.10 NAME 'monitorContext' DESC 'mon
 itor context' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.4.1.1466.115.121
 .1.12 SINGLE-VALUE NO-USER-MODIFICATION USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.1.12.2.1 NAME 'configContext' DESC 'conf
 ig context' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1
 .12 SINGLE-VALUE NO-USER-MODIFICATION USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.1.4 NAME 'vendorName' DESC 'RFC3045: name of impl
 ementation vendor' EQUALITY caseExactMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.
 15 SINGLE-VALUE NO-USER-MODIFICATION USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.1.5 NAME 'vendorVersion' DESC 'RFC3045: version o
 f implementation' EQUALITY caseExactMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.1
 5 SINGLE-VALUE NO-USER-MODIFICATION USAGE dSAOperation )
olcAttributeTypes: ( 2.5.18.5 NAME 'administrativeRole' DESC 'RFC3672: adminis
 trative role' EQUALITY objectIdentifierMatch SYNTAX 1.3.6.1.4.1.1466.115.121.
 1.38 USAGE directoryOperation )
olcAttributeTypes: ( 2.5.18.6 NAME 'subtreeSpecification' DESC 'RFC3672: subtr
 ee specification' SYNTAX 1.3.6.1.4.1.1466.115.121.1.45 SINGLE-VALUE USAGE dir
 ectoryOperation )
olcAttributeTypes: ( 2.5.21.1 NAME 'dITStructureRules' DESC 'RFC4512: DIT stru
 cture rules' EQUALITY integerFirstComponentMatch SYNTAX 1.3.6.1.4.1.1466.115.
 121.1.17 USAGE directoryOperation )
olcAttributeTypes: ( 2.5.21.2 NAME 'dITContentRules' DESC 'RFC4512: DIT conten
 t rules' EQUALITY objectIdentifierFirstComponentMatch SYNTAX 1.3.6.1.4.1.1466
 .115.121.1.16 USAGE directoryOperation )
olcAttributeTypes: ( 2.5.21.4 NAME 'matchingRules' DESC 'RFC4512: matching rul
 es' EQUALITY objectIdentifierFirstComponentMatch SYNTAX 1.3.6.1.4.1.1466.115.
 121.1.30 USAGE directoryOperation )
olcAttributeTypes: ( 2.5.21.5 NAME 'attributeTypes' DESC 'RFC4512: attribute t
 ypes' EQUALITY objectIdentifierFirstComponentMatch SYNTAX 1.3.6.1.4.1.1466.11
 5.121.1.3 USAGE directoryOperation )
olcAttributeTypes: ( 2.5.21.6 NAME 'objectClasses' DESC 'RFC4512: object class
 es' EQUALITY objectIdentifierFirstComponentMatch SYNTAX 1.3.6.1.4.1.1466.115.
 121.1.37 USAGE directoryOperation )
olcAttributeTypes: ( 2.5.21.7 NAME 'nameForms' DESC 'RFC4512: name forms ' EQU
 ALITY objectIdentifierFirstComponentMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.3
 5 USAGE directoryOperation )
olcAttributeTypes: ( 2.5.21.8 NAME 'matchingRuleUse' DESC 'RFC4512: matching r
 ule uses' EQUALITY objectIdentifierFirstComponentMatch SYNTAX 1.3.6.1.4.1.146
 6.115.121.1.31 USAGE directoryOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.120.16 NAME 'ldapSyntaxes' DESC 'RFC
 4512: LDAP syntaxes' EQUALITY objectIdentifierFirstComponentMatch SYNTAX 1.3.
 6.1.4.1.1466.115.121.1.54 USAGE directoryOperation )
olcAttributeTypes: ( 2.5.4.1 NAME ( 'aliasedObjectName' 'aliasedEntryName' ) D
 ESC 'RFC4512: name of aliased object' EQUALITY distinguishedNameMatch SYNTAX 
 1.3.6.1.4.1.1466.115.121.1.12 SINGLE-VALUE )
olcAttributeTypes: ( 2.16.840.1.113730.3.1.34 NAME 'ref' DESC 'RFC3296: subord
 inate referral URL' EQUALITY caseExactMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1
 .15 USAGE distributedOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.1.3.1 NAME 'entry' DESC 'OpenLDAP ACL en
 try pseudo-attribute' SYNTAX 1.3.6.1.4.1.4203.1.1.1 SINGLE-VALUE NO-USER-MODI
 FICATION USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.1.3.2 NAME 'children' DESC 'OpenLDAP ACL
  children pseudo-attribute' SYNTAX 1.3.6.1.4.1.4203.1.1.1 SINGLE-VALUE NO-USE
 R-MODIFICATION USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.666.1.8 NAME ( 'authzTo' 'saslAuthzTo' )
  DESC 'proxy authorization targets' EQUALITY authzMatch SYNTAX 1.3.6.1.4.1.42
 03.666.2.7 USAGE distributedOperation X-ORDERED 'VALUES' )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.666.1.9 NAME ( 'authzFrom' 'saslAuthzFro
 m' ) DESC 'proxy authorization sources' EQUALITY authzMatch SYNTAX 1.3.6.1.4.
 1.4203.666.2.7 USAGE distributedOperation X-ORDERED 'VALUES' )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.119.3 NAME 'entryTtl' DESC 'RFC2589:
  entry time-to-live' SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE NO-USE
 R-MODIFICATION USAGE dSAOperation )
olcAttributeTypes: ( 1.3.6.1.4.1.1466.101.119.4 NAME 'dynamicSubtrees' DESC 'R
 FC2589: dynamic subtrees' SYNTAX 1.3.6.1.4.1.1466.115.121.1.12 NO-USER-MODIFI
 CATION USAGE dSAOperation )
olcAttributeTypes: ( 2.5.4.49 NAME 'distinguishedName' DESC 'RFC4519: common s
 upertype of DN attributes' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.4.1
 .1466.115.121.1.12 )
olcAttributeTypes: ( 2.5.4.41 NAME 'name' DESC 'RFC4519: common supertype of n
 ame attributes' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYN
 TAX 1.3.6.1.4.1.1466.115.121.1.15{32768} )
olcAttributeTypes: ( 2.5.4.3 NAME ( 'cn' 'commonName' ) DESC 'RFC4519: common 
 name(s) for which the entity is known by' SUP name )
olcAttributeTypes: ( 0.9.2342.19200300.100.1.1 NAME ( 'uid' 'userid' ) DESC 'R
 FC4519: user identifier' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstrings
 Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: ( 1.3.6.1.1.1.1.0 NAME 'uidNumber' DESC 'RFC2307: An intege
 r uniquely identifying a user in an administrative domain' EQUALITY integerMa
 tch ORDERING integerOrderingMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE
 -VALUE )
olcAttributeTypes: ( 1.3.6.1.1.1.1.1 NAME 'gidNumber' DESC 'RFC2307: An intege
 r uniquely identifying a group in an administrative domain' EQUALITY integerM
 atch ORDERING integerOrderingMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGL
 E-VALUE )
olcAttributeTypes: ( 2.5.4.35 NAME 'userPassword' DESC 'RFC4519/2307: password
  of user' EQUALITY octetStringMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.40{128}
  )
olcAttributeTypes: ( 1.3.6.1.4.1.250.1.57 NAME 'labeledURI' DESC 'RFC2079: Uni
 form Resource Identifier with optional label' EQUALITY caseExactMatch SYNTAX 
 1.3.6.1.4.1.1466.115.121.1.15 )
olcAttributeTypes: ( 2.5.4.13 NAME 'description' DESC 'RFC4519: descriptive in
 formation' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX 1
 .3.6.1.4.1.1466.115.121.1.15{1024} )
olcAttributeTypes: ( 2.5.4.34 NAME 'seeAlso' DESC 'RFC4519: DN of related obje
 ct' SUP distinguishedName )
olcAttributeTypes: ( OLcfgGlAt:78 NAME 'olcConfigFile' DESC 'File for slapd co
 nfiguration directives' EQUALITY caseIgnoreMatch SYNTAX OMsDirectoryString SI
 NGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:79 NAME 'olcConfigDir' DESC 'Directory for slap
 d configuration backend' EQUALITY caseIgnoreMatch SYNTAX OMsDirectoryString S
 INGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:1 NAME 'olcAccess' DESC 'Access Control List' E
 QUALITY caseIgnoreMatch SYNTAX OMsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:86 NAME 'olcAddContentAcl' DESC 'Check ACLs aga
 inst content of Add ops' SYNTAX OMsBoolean SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:2 NAME 'olcAllows' DESC 'Allowed set of depreca
 ted features' EQUALITY caseIgnoreMatch SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:3 NAME 'olcArgsFile' DESC 'File for slapd comma
 nd line options' EQUALITY caseIgnoreMatch SYNTAX OMsDirectoryString SINGLE-VA
 LUE )
olcAttributeTypes: ( OLcfgGlAt:5 NAME 'olcAttributeOptions' EQUALITY caseIgnor
 eMatch SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:4 NAME 'olcAttributeTypes' DESC 'OpenLDAP attri
 buteTypes' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX O
 MsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:6 NAME 'olcAuthIDRewrite' EQUALITY caseIgnoreMa
 tch SYNTAX OMsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:7 NAME 'olcAuthzPolicy' EQUALITY caseIgnoreMatc
 h SYNTAX OMsDirectoryString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:8 NAME 'olcAuthzRegexp' EQUALITY caseIgnoreMatc
 h SYNTAX OMsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:9 NAME 'olcBackend' DESC 'A type of backend' EQ
 UALITY caseIgnoreMatch SYNTAX OMsDirectoryString SINGLE-VALUE X-ORDERED 'SIBL
 INGS' )
olcAttributeTypes: ( OLcfgGlAt:10 NAME 'olcConcurrency' SYNTAX OMsInteger SING
 LE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:11 NAME 'olcConnMaxPending' SYNTAX OMsInteger S
 INGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:12 NAME 'olcConnMaxPendingAuth' SYNTAX OMsInteg
 er SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:13 NAME 'olcDatabase' DESC 'The backend type fo
 r a database instance' SUP olcBackend SINGLE-VALUE X-ORDERED 'SIBLINGS' )
olcAttributeTypes: ( OLcfgGlAt:14 NAME 'olcDefaultSearchBase' SYNTAX OMsDN SIN
 GLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:15 NAME 'olcDisallows' EQUALITY caseIgnoreMatch
  SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:16 NAME 'olcDitContentRules' DESC 'OpenLDAP DIT
  content rules' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYN
 TAX OMsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgDbAt:0.20 NAME 'olcExtraAttrs' EQUALITY caseIgnoreMa
 tch SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:17 NAME 'olcGentleHUP' SYNTAX OMsBoolean SINGLE
 -VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.17 NAME 'olcHidden' SYNTAX OMsBoolean SINGLE-
 VALUE )
olcAttributeTypes: ( OLcfgGlAt:18 NAME 'olcIdleTimeout' SYNTAX OMsInteger SING
 LE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:19 NAME 'olcInclude' SUP labeledURI )
olcAttributeTypes: ( OLcfgGlAt:20 NAME 'olcIndexSubstrIfMinLen' SYNTAX OMsInte
 ger SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:21 NAME 'olcIndexSubstrIfMaxLen' SYNTAX OMsInte
 ger SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:22 NAME 'olcIndexSubstrAnyLen' SYNTAX OMsIntege
 r SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:23 NAME 'olcIndexSubstrAnyStep' SYNTAX OMsInteg
 er SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:84 NAME 'olcIndexIntLen' SYNTAX OMsInteger SING
 LE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.4 NAME 'olcLastMod' SYNTAX OMsBoolean SINGLE-
 VALUE )
olcAttributeTypes: ( OLcfgGlAt:85 NAME 'olcLdapSyntaxes' DESC 'OpenLDAP ldapSy
 ntax' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX OMsDir
 ectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgDbAt:0.5 NAME 'olcLimits' EQUALITY caseIgnoreMatch S
 YNTAX OMsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:93 NAME 'olcListenerThreads' SYNTAX OMsInteger 
 SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:26 NAME 'olcLocalSSF' SYNTAX OMsInteger SINGLE-
 VALUE )
olcAttributeTypes: ( OLcfgGlAt:27 NAME 'olcLogFile' SYNTAX OMsDirectoryString 
 SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:28 NAME 'olcLogLevel' EQUALITY caseIgnoreMatch 
 SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgDbAt:0.6 NAME 'olcMaxDerefDepth' SYNTAX OMsInteger S
 INGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.16 NAME 'olcMirrorMode' SYNTAX OMsBoolean SIN
 GLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:30 NAME 'olcModuleLoad' EQUALITY caseIgnoreMatc
 h SYNTAX OMsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:31 NAME 'olcModulePath' SYNTAX OMsDirectoryStri
 ng SINGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.18 NAME 'olcMonitoring' SYNTAX OMsBoolean SIN
 GLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:32 NAME 'olcObjectClasses' DESC 'OpenLDAP objec
 t classes' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX O
 MsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:33 NAME 'olcObjectIdentifier' EQUALITY caseIgno
 reMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX OMsDirectoryString X-ORDERED 
 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:34 NAME 'olcOverlay' SUP olcDatabase SINGLE-VAL
 UE X-ORDERED 'SIBLINGS' )
olcAttributeTypes: ( OLcfgGlAt:35 NAME 'olcPasswordCryptSaltFormat' SYNTAX OMs
 DirectoryString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:36 NAME 'olcPasswordHash' EQUALITY caseIgnoreMa
 tch SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:37 NAME 'olcPidFile' SYNTAX OMsDirectoryString 
 SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:38 NAME 'olcPlugin' EQUALITY caseIgnoreMatch SY
 NTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:39 NAME 'olcPluginLogFile' SYNTAX OMsDirectoryS
 tring SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:40 NAME 'olcReadOnly' SYNTAX OMsBoolean SINGLE-
 VALUE )
olcAttributeTypes: ( OLcfgGlAt:41 NAME 'olcReferral' SUP labeledURI SINGLE-VAL
 UE )
olcAttributeTypes: ( OLcfgDbAt:0.7 NAME 'olcReplica' SUP labeledURI EQUALITY c
 aseIgnoreMatch X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:43 NAME 'olcReplicaArgsFile' SYNTAX OMsDirector
 yString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:44 NAME 'olcReplicaPidFile' SYNTAX OMsDirectory
 String SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:45 NAME 'olcReplicationInterval' SYNTAX OMsInte
 ger SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:46 NAME 'olcReplogFile' SYNTAX OMsDirectoryStri
 ng SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:47 NAME 'olcRequires' EQUALITY caseIgnoreMatch 
 SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:48 NAME 'olcRestrict' EQUALITY caseIgnoreMatch 
 SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:49 NAME 'olcReverseLookup' SYNTAX OMsBoolean SI
 NGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.8 NAME 'olcRootDN' EQUALITY distinguishedName
 Match SYNTAX OMsDN SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:51 NAME 'olcRootDSE' EQUALITY caseIgnoreMatch S
 YNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgDbAt:0.9 NAME 'olcRootPW' SYNTAX OMsDirectoryString 
 SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:89 NAME 'olcSaslAuxprops' SYNTAX OMsDirectorySt
 ring SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:53 NAME 'olcSaslHost' SYNTAX OMsDirectoryString
  SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:54 NAME 'olcSaslRealm' SYNTAX OMsDirectoryStrin
 g SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:56 NAME 'olcSaslSecProps' SYNTAX OMsDirectorySt
 ring SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:58 NAME 'olcSchemaDN' EQUALITY distinguishedNam
 eMatch SYNTAX OMsDN SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:59 NAME 'olcSecurity' EQUALITY caseIgnoreMatch 
 SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:81 NAME 'olcServerID' EQUALITY caseIgnoreMatch 
 SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:60 NAME 'olcSizeLimit' SYNTAX OMsDirectoryStrin
 g SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:61 NAME 'olcSockbufMaxIncoming' SYNTAX OMsInteg
 er SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:62 NAME 'olcSockbufMaxIncomingAuth' SYNTAX OMsI
 nteger SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:83 NAME 'olcSortVals' DESC 'Attributes whose va
 lues will always be sorted' EQUALITY caseIgnoreMatch SYNTAX OMsDirectoryStrin
 g )
olcAttributeTypes: ( OLcfgDbAt:0.15 NAME 'olcSubordinate' SYNTAX OMsDirectoryS
 tring SINGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.10 NAME 'olcSuffix' EQUALITY distinguishedNam
 eMatch SYNTAX OMsDN )
olcAttributeTypes: ( OLcfgDbAt:0.19 NAME 'olcSyncUseSubentry' DESC 'Store sync
  context in a subentry' SYNTAX OMsBoolean SINGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.11 NAME 'olcSyncrepl' EQUALITY caseIgnoreMatc
 h SYNTAX OMsDirectoryString X-ORDERED 'VALUES' )
olcAttributeTypes: ( OLcfgGlAt:90 NAME 'olcTCPBuffer' DESC 'Custom TCP buffer 
 size' SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgGlAt:66 NAME 'olcThreads' SYNTAX OMsInteger SINGLE-V
 ALUE )
olcAttributeTypes: ( OLcfgGlAt:67 NAME 'olcTimeLimit' SYNTAX OMsDirectoryStrin
 g )
olcAttributeTypes: ( OLcfgGlAt:68 NAME 'olcTLSCACertificateFile' SYNTAX OMsDir
 ectoryString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:69 NAME 'olcTLSCACertificatePath' SYNTAX OMsDir
 ectoryString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:70 NAME 'olcTLSCertificateFile' SYNTAX OMsDirec
 toryString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:71 NAME 'olcTLSCertificateKeyFile' SYNTAX OMsDi
 rectoryString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:72 NAME 'olcTLSCipherSuite' SYNTAX OMsDirectory
 String SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:73 NAME 'olcTLSCRLCheck' SYNTAX OMsDirectoryStr
 ing SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:82 NAME 'olcTLSCRLFile' SYNTAX OMsDirectoryStri
 ng SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:74 NAME 'olcTLSRandFile' SYNTAX OMsDirectoryStr
 ing SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:75 NAME 'olcTLSVerifyClient' SYNTAX OMsDirector
 yString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:77 NAME 'olcTLSDHParamFile' SYNTAX OMsDirectory
 String SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:96 NAME 'olcTLSECName' SYNTAX OMsDirectoryStrin
 g SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:87 NAME 'olcTLSProtocolMin' SYNTAX OMsDirectory
 String SINGLE-VALUE )
olcAttributeTypes: ( OLcfgGlAt:80 NAME 'olcToolThreads' SYNTAX OMsInteger SING
 LE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.12 NAME 'olcUpdateDN' SYNTAX OMsDN SINGLE-VAL
 UE )
olcAttributeTypes: ( OLcfgDbAt:0.13 NAME 'olcUpdateRef' SUP labeledURI EQUALIT
 Y caseIgnoreMatch )
olcAttributeTypes: ( OLcfgGlAt:88 NAME 'olcWriteTimeout' SYNTAX OMsInteger SIN
 GLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.1 NAME 'olcDbDirectory' DESC 'Directory for d
 atabase content' EQUALITY caseIgnoreMatch SYNTAX OMsDirectoryString SINGLE-VA
 LUE )
olcAttributeTypes: ( 1.3.6.1.4.1.4203.666.1.5 NAME 'OpenLDAPaci' DESC 'OpenLDA
 P access control information (experimental)' EQUALITY OpenLDAPaciMatch SYNTAX
  1.3.6.1.4.1.4203.666.2.1 USAGE directoryOperation )
olcAttributeTypes: ( OLcfgDbAt:1.2 NAME 'olcDbCheckpoint' DESC 'Database check
 point interval in kbytes and minutes' SYNTAX OMsDirectoryString SINGLE-VALUE 
 )
olcAttributeTypes: ( OLcfgDbAt:1.4 NAME 'olcDbNoSync' DESC 'Disable synchronou
 s database writes' SYNTAX OMsBoolean SINGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:12.3 NAME 'olcDbEnvFlags' DESC 'Database enviro
 nment flags' EQUALITY caseIgnoreMatch SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgDbAt:0.2 NAME 'olcDbIndex' DESC 'Attribute index par
 ameters' EQUALITY caseIgnoreMatch SYNTAX OMsDirectoryString )
olcAttributeTypes: ( OLcfgDbAt:12.1 NAME 'olcDbMaxReaders' DESC 'Maximum numbe
 r of threads that may access the DB concurrently' SYNTAX OMsInteger SINGLE-VA
 LUE )
olcAttributeTypes: ( OLcfgDbAt:12.2 NAME 'olcDbMaxSize' DESC 'Maximum size of 
 DB in bytes' SYNTAX OMsInteger SINGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:0.3 NAME 'olcDbMode' DESC 'Unix permissions of 
 database files' SYNTAX OMsDirectoryString SINGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:12.5 NAME 'olcDbRtxnSize' DESC 'Number of entri
 es to process in one read transaction' SYNTAX OMsInteger SINGLE-VALUE )
olcAttributeTypes: ( OLcfgDbAt:1.9 NAME 'olcDbSearchStack' DESC 'Depth of sear
 ch stack in IDLs' SYNTAX OMsInteger SINGLE-VALUE )
olcObjectClasses: ( 2.5.6.0 NAME 'top' DESC 'top of the superclass chain' ABST
 RACT MUST objectClass )
olcObjectClasses: ( 1.3.6.1.4.1.1466.101.120.111 NAME 'extensibleObject' DESC 
 'RFC4512: extensible object' SUP top AUXILIARY )
olcObjectClasses: ( 2.5.6.1 NAME 'alias' DESC 'RFC4512: an alias' SUP top STRU
 CTURAL MUST aliasedObjectName )
olcObjectClasses: ( 2.16.840.1.113730.3.2.6 NAME 'referral' DESC 'namedref: na
 med subordinate referral' SUP top STRUCTURAL MUST ref )
olcObjectClasses: ( 1.3.6.1.4.1.4203.1.4.1 NAME ( 'OpenLDAProotDSE' 'LDAProotD
 SE' ) DESC 'OpenLDAP Root DSE object' SUP top STRUCTURAL MAY cn )
olcObjectClasses: ( 2.5.17.0 NAME 'subentry' DESC 'RFC3672: subentry' SUP top 
 STRUCTURAL MUST ( cn $ subtreeSpecification ) )
olcObjectClasses: ( 2.5.20.1 NAME 'subschema' DESC 'RFC4512: controlling subsc
 hema (sub)entry' AUXILIARY MAY ( dITStructureRules $ nameForms $ dITContentRu
 les $ objectClasses $ attributeTypes $ matchingRules $ matchingRuleUse ) )
olcObjectClasses: ( 1.3.6.1.4.1.1466.101.119.2 NAME 'dynamicObject' DESC 'RFC2
 589: Dynamic Object' SUP top AUXILIARY )
olcObjectClasses: ( 1.3.6.1.4.1.4203.666.3.4 NAME 'glue' DESC 'Glue Entry' SUP
  top STRUCTURAL )
olcObjectClasses: ( 1.3.6.1.4.1.4203.666.3.5 NAME 'syncConsumerSubentry' DESC 
 'Persistent Info for SyncRepl Consumer' AUXILIARY MAY syncreplCookie )
olcObjectClasses: ( 1.3.6.1.4.1.4203.666.3.6 NAME 'syncProviderSubentry' DESC 
 'Persistent Info for SyncRepl Producer' AUXILIARY MAY contextCSN )
olcObjectClasses: ( OLcfgGlOc:0 NAME 'olcConfig' DESC 'OpenLDAP configuration 
 object' SUP top ABSTRACT )
olcObjectClasses: ( OLcfgGlOc:1 NAME 'olcGlobal' DESC 'OpenLDAP Global configu
 ration options' SUP olcConfig STRUCTURAL MAY ( cn $ olcConfigFile $ olcConfig
 Dir $ olcAllows $ olcArgsFile $ olcAttributeOptions $ olcAuthIDRewrite $ olcA
 uthzPolicy $ olcAuthzRegexp $ olcConcurrency $ olcConnMaxPending $ olcConnMax
 PendingAuth $ olcDisallows $ olcGentleHUP $ olcIdleTimeout $ olcIndexSubstrIf
 MaxLen $ olcIndexSubstrIfMinLen $ olcIndexSubstrAnyLen $ olcIndexSubstrAnySte
 p $ olcIndexIntLen $ olcListenerThreads $ olcLocalSSF $ olcLogFile $ olcLogLe
 vel $ olcPasswordCryptSaltFormat $ olcPasswordHash $ olcPidFile $ olcPluginLo
 gFile $ olcReadOnly $ olcReferral $ olcReplogFile $ olcRequires $ olcRestrict
  $ olcReverseLookup $ olcRootDSE $ olcSaslAuxprops $ olcSaslHost $ olcSaslRea
 lm $ olcSaslSecProps $ olcSecurity $ olcServerID $ olcSizeLimit $ olcSockbufM
 axIncoming $ olcSockbufMaxIncomingAuth $ olcTCPBuffer $ olcThreads $ olcTimeL
 imit $ olcTLSCACertificateFile $ olcTLSCACertificatePath $ olcTLSCertificateF
 ile $ olcTLSCertificateKeyFile $ olcTLSCipherSuite $ olcTLSCRLCheck $ olcTLSR
 andFile $ olcTLSVerifyClient $ olcTLSDHParamFile $ olcTLSECName $ olcTLSCRLFi
 le $ olcTLSProtocolMin $ olcToolThreads $ olcWriteTimeout $ olcObjectIdentifi
 er $ olcAttributeTypes $ olcObjectClasses $ olcDitContentRules $ olcLdapSynta
 xes ) )
olcObjectClasses: ( OLcfgGlOc:2 NAME 'olcSchemaConfig' DESC 'OpenLDAP schema o
 bject' SUP olcConfig STRUCTURAL MAY ( cn $ olcObjectIdentifier $ olcLdapSynta
 xes $ olcAttributeTypes $ olcObjectClasses $ olcDitContentRules ) )
olcObjectClasses: ( OLcfgGlOc:3 NAME 'olcBackendConfig' DESC 'OpenLDAP Backend
 -specific options' SUP olcConfig STRUCTURAL MUST olcBackend )
olcObjectClasses: ( OLcfgGlOc:4 NAME 'olcDatabaseConfig' DESC 'OpenLDAP Databa
 se-specific options' SUP olcConfig STRUCTURAL MUST olcDatabase MAY ( olcHidde
 n $ olcSuffix $ olcSubordinate $ olcAccess $ olcAddContentAcl $ olcLastMod $ 
 olcLimits $ olcMaxDerefDepth $ olcPlugin $ olcReadOnly $ olcReplica $ olcRepl
 icaArgsFile $ olcReplicaPidFile $ olcReplicationInterval $ olcReplogFile $ ol
 cRequires $ olcRestrict $ olcRootDN $ olcRootPW $ olcSchemaDN $ olcSecurity $
  olcSizeLimit $ olcSyncUseSubentry $ olcSyncrepl $ olcTimeLimit $ olcUpdateDN
  $ olcUpdateRef $ olcMirrorMode $ olcMonitoring $ olcExtraAttrs ) )
olcObjectClasses: ( OLcfgGlOc:5 NAME 'olcOverlayConfig' DESC 'OpenLDAP Overlay
 -specific options' SUP olcConfig STRUCTURAL MUST olcOverlay )
olcObjectClasses: ( OLcfgGlOc:6 NAME 'olcIncludeFile' DESC 'OpenLDAP configura
 tion include file' SUP olcConfig STRUCTURAL MUST olcInclude MAY ( cn $ olcRoo
 tDSE ) )
olcObjectClasses: ( OLcfgGlOc:7 NAME 'olcFrontendConfig' DESC 'OpenLDAP fronte
 nd configuration' AUXILIARY MAY ( olcDefaultSearchBase $ olcPasswordHash $ ol
 cSortVals ) )
olcObjectClasses: ( OLcfgGlOc:8 NAME 'olcModuleList' DESC 'OpenLDAP dynamic mo
 dule info' SUP olcConfig STRUCTURAL MAY ( cn $ olcModulePath $ olcModuleLoad 
 ) )
olcObjectClasses: ( OLcfgDbOc:2.1 NAME 'olcLdifConfig' DESC 'LDIF backend conf
 iguration' SUP olcDatabaseConfig STRUCTURAL MUST olcDbDirectory )
olcObjectClasses: ( OLcfgDbOc:12.1 NAME 'olcMdbConfig' DESC 'MDB backend confi
 guration' SUP olcDatabaseConfig STRUCTURAL MUST olcDbDirectory MAY ( olcDbChe
 ckpoint $ olcDbEnvFlags $ olcDbNoSync $ olcDbIndex $ olcDbMaxReaders $ olcDbM
 axSize $ olcDbMode $ olcDbSearchStack $ olcDbRtxnSize ) )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.1 DESC 'ACI Item' X-BINARY-TRANS
 FER-REQUIRED 'TRUE' X-NOT-HUMAN-READABLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.2 DESC 'Access Point' X-NOT-HUMA
 N-READABLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.3 DESC 'Attribute Type Descripti
 on' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.4 DESC 'Audio' X-NOT-HUMAN-READA
 BLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.5 DESC 'Binary' X-NOT-HUMAN-READ
 ABLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.6 DESC 'Bit String' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.7 DESC 'Boolean' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.8 DESC 'Certificate' X-BINARY-TR
 ANSFER-REQUIRED 'TRUE' X-NOT-HUMAN-READABLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.9 DESC 'Certificate List' X-BINA
 RY-TRANSFER-REQUIRED 'TRUE' X-NOT-HUMAN-READABLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.10 DESC 'Certificate Pair' X-BIN
 ARY-TRANSFER-REQUIRED 'TRUE' X-NOT-HUMAN-READABLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.4203.666.11.10.2.1 DESC 'X.509 AttributeCertifi
 cate' X-BINARY-TRANSFER-REQUIRED 'TRUE' X-NOT-HUMAN-READABLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.12 DESC 'Distinguished Name' )
olcLdapSyntaxes: ( 1.2.36.79672281.1.5.0 DESC 'RDN' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.13 DESC 'Data Quality' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.14 DESC 'Delivery Method' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.15 DESC 'Directory String' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.16 DESC 'DIT Content Rule Descri
 ption' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.17 DESC 'DIT Structure Rule Desc
 ription' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.19 DESC 'DSA Quality' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.20 DESC 'DSE Type' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.21 DESC 'Enhanced Guide' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.22 DESC 'Facsimile Telephone Num
 ber' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.23 DESC 'Fax' X-NOT-HUMAN-READAB
 LE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.24 DESC 'Generalized Time' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.25 DESC 'Guide' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.26 DESC 'IA5 String' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.27 DESC 'Integer' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.28 DESC 'JPEG' X-NOT-HUMAN-READA
 BLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.29 DESC 'Master And Shadow Acces
 s Points' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.30 DESC 'Matching Rule Descripti
 on' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.31 DESC 'Matching Rule Use Descr
 iption' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.32 DESC 'Mail Preference' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.33 DESC 'MHS OR Address' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.34 DESC 'Name And Optional UID' 
 )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.35 DESC 'Name Form Description' 
 )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.36 DESC 'Numeric String' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.37 DESC 'Object Class Descriptio
 n' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.38 DESC 'OID' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.39 DESC 'Other Mailbox' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.40 DESC 'Octet String' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.41 DESC 'Postal Address' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.42 DESC 'Protocol Information' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.43 DESC 'Presentation Address' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.44 DESC 'Printable String' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.11 DESC 'Country String' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.45 DESC 'SubtreeSpecification' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.49 DESC 'Supported Algorithm' X-
 BINARY-TRANSFER-REQUIRED 'TRUE' X-NOT-HUMAN-READABLE 'TRUE' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.50 DESC 'Telephone Number' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.51 DESC 'Teletex Terminal Identi
 fier' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.52 DESC 'Telex Number' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.54 DESC 'LDAP Syntax Description
 ' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.55 DESC 'Modify Rights' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.56 DESC 'LDAP Schema Definition'
  )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.57 DESC 'LDAP Schema Description
 ' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.1466.115.121.1.58 DESC 'Substring Assertion' )
olcLdapSyntaxes: ( 1.3.6.1.1.1.0.0 DESC 'RFC2307 NIS Netgroup Triple' )
olcLdapSyntaxes: ( 1.3.6.1.1.1.0.1 DESC 'RFC2307 Boot Parameter' )
olcLdapSyntaxes: ( 1.3.6.1.1.15.1 DESC 'Certificate Exact Assertion' )
olcLdapSyntaxes: ( 1.3.6.1.1.15.2 DESC 'Certificate Assertion' )
olcLdapSyntaxes: ( 1.3.6.1.1.15.3 DESC 'Certificate Pair Exact Assertion' )
olcLdapSyntaxes: ( 1.3.6.1.1.15.4 DESC 'Certificate Pair Assertion' )
olcLdapSyntaxes: ( 1.3.6.1.1.15.5 DESC 'Certificate List Exact Assertion' )
olcLdapSyntaxes: ( 1.3.6.1.1.15.6 DESC 'Certificate List Assertion' )
olcLdapSyntaxes: ( 1.3.6.1.1.15.7 DESC 'Algorithm Identifier' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.4203.666.11.10.2.2 DESC 'AttributeCertificate E
 xact Assertion' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.4203.666.11.10.2.3 DESC 'AttributeCertificate A
 ssertion' )
olcLdapSyntaxes: ( 1.3.6.1.1.16.1 DESC 'UUID' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.4203.666.11.2.1 DESC 'CSN' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.4203.666.11.2.4 DESC 'CSN SID' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.4203.1.1.1 DESC 'OpenLDAP void' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.4203.666.2.7 DESC 'OpenLDAP authz' )
olcLdapSyntaxes: ( 1.3.6.1.4.1.4203.666.2.1 DESC 'OpenLDAP Experimental ACI' )

# {0}core, schema, config
dn: cn={0}core,cn=schema,cn=config
objectClass: olcSchemaConfig
cn: {0}core
olcAttributeTypes: {0}( 2.5.4.2 NAME 'knowledgeInformation' DESC 'RFC2256: kno
 wledge information' EQUALITY caseIgnoreMatch SYNTAX 1.3.6.1.4.1.1466.115.121.
 1.15{32768} )
olcAttributeTypes: {1}( 2.5.4.4 NAME ( 'sn' 'surname' ) DESC 'RFC2256: last (f
 amily) name(s) for which the entity is known by' SUP name )
olcAttributeTypes: {2}( 2.5.4.5 NAME 'serialNumber' DESC 'RFC2256: serial numb
 er of the entity' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch S
 YNTAX 1.3.6.1.4.1.1466.115.121.1.44{64} )
olcAttributeTypes: {3}( 2.5.4.6 NAME ( 'c' 'countryName' ) DESC 'RFC4519: two-
 letter ISO-3166 country code' SUP name SYNTAX 1.3.6.1.4.1.1466.115.121.1.11 S
 INGLE-VALUE )
olcAttributeTypes: {4}( 2.5.4.7 NAME ( 'l' 'localityName' ) DESC 'RFC2256: loc
 ality which this object resides in' SUP name )
olcAttributeTypes: {5}( 2.5.4.8 NAME ( 'st' 'stateOrProvinceName' ) DESC 'RFC2
 256: state or province which this object resides in' SUP name )
olcAttributeTypes: {6}( 2.5.4.9 NAME ( 'street' 'streetAddress' ) DESC 'RFC225
 6: street address of this object' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreS
 ubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{128} )
olcAttributeTypes: {7}( 2.5.4.10 NAME ( 'o' 'organizationName' ) DESC 'RFC2256
 : organization this object belongs to' SUP name )
olcAttributeTypes: {8}( 2.5.4.11 NAME ( 'ou' 'organizationalUnitName' ) DESC '
 RFC2256: organizational unit this object belongs to' SUP name )
olcAttributeTypes: {9}( 2.5.4.12 NAME 'title' DESC 'RFC2256: title associated 
 with the entity' SUP name )
olcAttributeTypes: {10}( 2.5.4.14 NAME 'searchGuide' DESC 'RFC2256: search gui
 de, deprecated by enhancedSearchGuide' SYNTAX 1.3.6.1.4.1.1466.115.121.1.25 )
olcAttributeTypes: {11}( 2.5.4.15 NAME 'businessCategory' DESC 'RFC2256: busin
 ess category' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTA
 X 1.3.6.1.4.1.1466.115.121.1.15{128} )
olcAttributeTypes: {12}( 2.5.4.16 NAME 'postalAddress' DESC 'RFC2256: postal a
 ddress' EQUALITY caseIgnoreListMatch SUBSTR caseIgnoreListSubstringsMatch SYN
 TAX 1.3.6.1.4.1.1466.115.121.1.41 )
olcAttributeTypes: {13}( 2.5.4.17 NAME 'postalCode' DESC 'RFC2256: postal code
 ' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.
 1.1466.115.121.1.15{40} )
olcAttributeTypes: {14}( 2.5.4.18 NAME 'postOfficeBox' DESC 'RFC2256: Post Off
 ice Box' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX 1.3
 .6.1.4.1.1466.115.121.1.15{40} )
olcAttributeTypes: {15}( 2.5.4.19 NAME 'physicalDeliveryOfficeName' DESC 'RFC2
 256: Physical Delivery Office Name' EQUALITY caseIgnoreMatch SUBSTR caseIgnor
 eSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{128} )
olcAttributeTypes: {16}( 2.5.4.20 NAME 'telephoneNumber' DESC 'RFC2256: Teleph
 one Number' EQUALITY telephoneNumberMatch SUBSTR telephoneNumberSubstringsMat
 ch SYNTAX 1.3.6.1.4.1.1466.115.121.1.50{32} )
olcAttributeTypes: {17}( 2.5.4.21 NAME 'telexNumber' DESC 'RFC2256: Telex Numb
 er' SYNTAX 1.3.6.1.4.1.1466.115.121.1.52 )
olcAttributeTypes: {18}( 2.5.4.22 NAME 'teletexTerminalIdentifier' DESC 'RFC22
 56: Teletex Terminal Identifier' SYNTAX 1.3.6.1.4.1.1466.115.121.1.51 )
olcAttributeTypes: {19}( 2.5.4.23 NAME ( 'facsimileTelephoneNumber' 'fax' ) DE
 SC 'RFC2256: Facsimile (Fax) Telephone Number' SYNTAX 1.3.6.1.4.1.1466.115.12
 1.1.22 )
olcAttributeTypes: {20}( 2.5.4.24 NAME 'x121Address' DESC 'RFC2256: X.121 Addr
 ess' EQUALITY numericStringMatch SUBSTR numericStringSubstringsMatch SYNTAX 1
 .3.6.1.4.1.1466.115.121.1.36{15} )
olcAttributeTypes: {21}( 2.5.4.25 NAME 'internationaliSDNNumber' DESC 'RFC2256
 : international ISDN number' EQUALITY numericStringMatch SUBSTR numericString
 SubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.36{16} )
olcAttributeTypes: {22}( 2.5.4.26 NAME 'registeredAddress' DESC 'RFC2256: regi
 stered postal address' SUP postalAddress SYNTAX 1.3.6.1.4.1.1466.115.121.1.41
  )
olcAttributeTypes: {23}( 2.5.4.27 NAME 'destinationIndicator' DESC 'RFC2256: d
 estination indicator' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMat
 ch SYNTAX 1.3.6.1.4.1.1466.115.121.1.44{128} )
olcAttributeTypes: {24}( 2.5.4.28 NAME 'preferredDeliveryMethod' DESC 'RFC2256
 : preferred delivery method' SYNTAX 1.3.6.1.4.1.1466.115.121.1.14 SINGLE-VALU
 E )
olcAttributeTypes: {25}( 2.5.4.29 NAME 'presentationAddress' DESC 'RFC2256: pr
 esentation address' EQUALITY presentationAddressMatch SYNTAX 1.3.6.1.4.1.1466
 .115.121.1.43 SINGLE-VALUE )
olcAttributeTypes: {26}( 2.5.4.30 NAME 'supportedApplicationContext' DESC 'RFC
 2256: supported application context' EQUALITY objectIdentifierMatch SYNTAX 1.
 3.6.1.4.1.1466.115.121.1.38 )
olcAttributeTypes: {27}( 2.5.4.31 NAME 'member' DESC 'RFC2256: member of a gro
 up' SUP distinguishedName )
olcAttributeTypes: {28}( 2.5.4.32 NAME 'owner' DESC 'RFC2256: owner (of the ob
 ject)' SUP distinguishedName )
olcAttributeTypes: {29}( 2.5.4.33 NAME 'roleOccupant' DESC 'RFC2256: occupant 
 of role' SUP distinguishedName )
olcAttributeTypes: {30}( 2.5.4.36 NAME 'userCertificate' DESC 'RFC2256: X.509 
 user certificate, use ;binary' EQUALITY certificateExactMatch SYNTAX 1.3.6.1.
 4.1.1466.115.121.1.8 )
olcAttributeTypes: {31}( 2.5.4.37 NAME 'cACertificate' DESC 'RFC2256: X.509 CA
  certificate, use ;binary' EQUALITY certificateExactMatch SYNTAX 1.3.6.1.4.1.
 1466.115.121.1.8 )
olcAttributeTypes: {32}( 2.5.4.38 NAME 'authorityRevocationList' DESC 'RFC2256
 : X.509 authority revocation list, use ;binary' SYNTAX 1.3.6.1.4.1.1466.115.1
 21.1.9 )
olcAttributeTypes: {33}( 2.5.4.39 NAME 'certificateRevocationList' DESC 'RFC22
 56: X.509 certificate revocation list, use ;binary' SYNTAX 1.3.6.1.4.1.1466.1
 15.121.1.9 )
olcAttributeTypes: {34}( 2.5.4.40 NAME 'crossCertificatePair' DESC 'RFC2256: X
 .509 cross certificate pair, use ;binary' SYNTAX 1.3.6.1.4.1.1466.115.121.1.1
 0 )
olcAttributeTypes: {35}( 2.5.4.42 NAME ( 'givenName' 'gn' ) DESC 'RFC2256: fir
 st name(s) for which the entity is known by' SUP name )
olcAttributeTypes: {36}( 2.5.4.43 NAME 'initials' DESC 'RFC2256: initials of s
 ome or all of names, but not the surname(s).' SUP name )
olcAttributeTypes: {37}( 2.5.4.44 NAME 'generationQualifier' DESC 'RFC2256: na
 me qualifier indicating a generation' SUP name )
olcAttributeTypes: {38}( 2.5.4.45 NAME 'x500UniqueIdentifier' DESC 'RFC2256: X
 .500 unique identifier' EQUALITY bitStringMatch SYNTAX 1.3.6.1.4.1.1466.115.1
 21.1.6 )
olcAttributeTypes: {39}( 2.5.4.46 NAME 'dnQualifier' DESC 'RFC2256: DN qualifi
 er' EQUALITY caseIgnoreMatch ORDERING caseIgnoreOrderingMatch SUBSTR caseIgno
 reSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.44 )
olcAttributeTypes: {40}( 2.5.4.47 NAME 'enhancedSearchGuide' DESC 'RFC2256: en
 hanced search guide' SYNTAX 1.3.6.1.4.1.1466.115.121.1.21 )
olcAttributeTypes: {41}( 2.5.4.48 NAME 'protocolInformation' DESC 'RFC2256: pr
 otocol information' EQUALITY protocolInformationMatch SYNTAX 1.3.6.1.4.1.1466
 .115.121.1.42 )
olcAttributeTypes: {42}( 2.5.4.50 NAME 'uniqueMember' DESC 'RFC2256: unique me
 mber of a group' EQUALITY uniqueMemberMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1
 .34 )
olcAttributeTypes: {43}( 2.5.4.51 NAME 'houseIdentifier' DESC 'RFC2256: house 
 identifier' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX 
 1.3.6.1.4.1.1466.115.121.1.15{32768} )
olcAttributeTypes: {44}( 2.5.4.52 NAME 'supportedAlgorithms' DESC 'RFC2256: su
 pported algorithms' SYNTAX 1.3.6.1.4.1.1466.115.121.1.49 )
olcAttributeTypes: {45}( 2.5.4.53 NAME 'deltaRevocationList' DESC 'RFC2256: de
 lta revocation list; use ;binary' SYNTAX 1.3.6.1.4.1.1466.115.121.1.9 )
olcAttributeTypes: {46}( 2.5.4.54 NAME 'dmdName' DESC 'RFC2256: name of DMD' S
 UP name )
olcAttributeTypes: {47}( 2.5.4.65 NAME 'pseudonym' DESC 'X.520(4th): pseudonym
  for the object' SUP name )
olcAttributeTypes: {48}( 0.9.2342.19200300.100.1.3 NAME ( 'mail' 'rfc822Mailbo
 x' ) DESC 'RFC1274: RFC822 Mailbox'   EQUALITY caseIgnoreIA5Match   SUBSTR ca
 seIgnoreIA5SubstringsMatch   SYNTAX 1.3.6.1.4.1.1466.115.121.1.26{256} )
olcAttributeTypes: {49}( 0.9.2342.19200300.100.1.25 NAME ( 'dc' 'domainCompone
 nt' ) DESC 'RFC1274/2247: domain component' EQUALITY caseIgnoreIA5Match SUBST
 R caseIgnoreIA5SubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 SINGLE-VA
 LUE )
olcAttributeTypes: {50}( 0.9.2342.19200300.100.1.37 NAME 'associatedDomain' DE
 SC 'RFC1274: domain associated with object' EQUALITY caseIgnoreIA5Match SUBST
 R caseIgnoreIA5SubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
olcAttributeTypes: {51}( 1.2.840.113549.1.9.1 NAME ( 'email' 'emailAddress' 'p
 kcs9email' ) DESC 'RFC3280: legacy attribute for email addresses in DNs' EQUA
 LITY caseIgnoreIA5Match SUBSTR caseIgnoreIA5SubstringsMatch SYNTAX 1.3.6.1.4.
 1.1466.115.121.1.26{128} )
olcObjectClasses: {0}( 2.5.6.2 NAME 'country' DESC 'RFC2256: a country' SUP to
 p STRUCTURAL MUST c MAY ( searchGuide $ description ) )
olcObjectClasses: {1}( 2.5.6.3 NAME 'locality' DESC 'RFC2256: a locality' SUP 
 top STRUCTURAL MAY ( street $ seeAlso $ searchGuide $ st $ l $ description ) 
 )
olcObjectClasses: {2}( 2.5.6.4 NAME 'organization' DESC 'RFC2256: an organizat
 ion' SUP top STRUCTURAL MUST o MAY ( userPassword $ searchGuide $ seeAlso $ b
 usinessCategory $ x121Address $ registeredAddress $ destinationIndicator $ pr
 eferredDeliveryMethod $ telexNumber $ teletexTerminalIdentifier $ telephoneNu
 mber $ internationaliSDNNumber $  facsimileTelephoneNumber $ street $ postOff
 iceBox $ postalCode $ postalAddress $ physicalDeliveryOfficeName $ st $ l $ d
 escription ) )
olcObjectClasses: {3}( 2.5.6.5 NAME 'organizationalUnit' DESC 'RFC2256: an org
 anizational unit' SUP top STRUCTURAL MUST ou MAY ( userPassword $ searchGuide
  $ seeAlso $ businessCategory $ x121Address $ registeredAddress $ destination
 Indicator $ preferredDeliveryMethod $ telexNumber $ teletexTerminalIdentifier
  $ telephoneNumber $ internationaliSDNNumber $ facsimileTelephoneNumber $ str
 eet $ postOfficeBox $ postalCode $ postalAddress $ physicalDeliveryOfficeName
  $ st $ l $ description ) )
olcObjectClasses: {4}( 2.5.6.6 NAME 'person' DESC 'RFC2256: a person' SUP top 
 STRUCTURAL MUST ( sn $ cn ) MAY ( userPassword $ telephoneNumber $ seeAlso $ 
 description ) )
olcObjectClasses: {5}( 2.5.6.7 NAME 'organizationalPerson' DESC 'RFC2256: an o
 rganizational person' SUP person STRUCTURAL MAY ( title $ x121Address $ regis
 teredAddress $ destinationIndicator $ preferredDeliveryMethod $ telexNumber $
  teletexTerminalIdentifier $ telephoneNumber $ internationaliSDNNumber $  fac
 simileTelephoneNumber $ street $ postOfficeBox $ postalCode $ postalAddress $
  physicalDeliveryOfficeName $ ou $ st $ l ) )
olcObjectClasses: {6}( 2.5.6.8 NAME 'organizationalRole' DESC 'RFC2256: an org
 anizational role' SUP top STRUCTURAL MUST cn MAY ( x121Address $ registeredAd
 dress $ destinationIndicator $ preferredDeliveryMethod $ telexNumber $ telete
 xTerminalIdentifier $ telephoneNumber $ internationaliSDNNumber $ facsimileTe
 lephoneNumber $ seeAlso $ roleOccupant $ preferredDeliveryMethod $ street $ p
 ostOfficeBox $ postalCode $ postalAddress $ physicalDeliveryOfficeName $ ou $
  st $ l $ description ) )
olcObjectClasses: {7}( 2.5.6.9 NAME 'groupOfNames' DESC 'RFC2256: a group of n
 ames (DNs)' SUP top STRUCTURAL MUST ( member $ cn ) MAY ( businessCategory $ 
 seeAlso $ owner $ ou $ o $ description ) )
olcObjectClasses: {8}( 2.5.6.10 NAME 'residentialPerson' DESC 'RFC2256: an res
 idential person' SUP person STRUCTURAL MUST l MAY ( businessCategory $ x121Ad
 dress $ registeredAddress $ destinationIndicator $ preferredDeliveryMethod $ 
 telexNumber $ teletexTerminalIdentifier $ telephoneNumber $ internationaliSDN
 Number $ facsimileTelephoneNumber $ preferredDeliveryMethod $ street $ postOf
 ficeBox $ postalCode $ postalAddress $ physicalDeliveryOfficeName $ st $ l ) 
 )
olcObjectClasses: {9}( 2.5.6.11 NAME 'applicationProcess' DESC 'RFC2256: an ap
 plication process' SUP top STRUCTURAL MUST cn MAY ( seeAlso $ ou $ l $ descri
 ption ) )
olcObjectClasses: {10}( 2.5.6.12 NAME 'applicationEntity' DESC 'RFC2256: an ap
 plication entity' SUP top STRUCTURAL MUST ( presentationAddress $ cn ) MAY ( 
 supportedApplicationContext $ seeAlso $ ou $ o $ l $ description ) )
olcObjectClasses: {11}( 2.5.6.13 NAME 'dSA' DESC 'RFC2256: a directory system 
 agent (a server)' SUP applicationEntity STRUCTURAL MAY knowledgeInformation )
olcObjectClasses: {12}( 2.5.6.14 NAME 'device' DESC 'RFC2256: a device' SUP to
 p STRUCTURAL MUST cn MAY ( serialNumber $ seeAlso $ owner $ ou $ o $ l $ desc
 ription ) )
olcObjectClasses: {13}( 2.5.6.15 NAME 'strongAuthenticationUser' DESC 'RFC2256
 : a strong authentication user' SUP top AUXILIARY MUST userCertificate )
olcObjectClasses: {14}( 2.5.6.16 NAME 'certificationAuthority' DESC 'RFC2256: 
 a certificate authority' SUP top AUXILIARY MUST ( authorityRevocationList $ c
 ertificateRevocationList $ cACertificate ) MAY crossCertificatePair )
olcObjectClasses: {15}( 2.5.6.17 NAME 'groupOfUniqueNames' DESC 'RFC2256: a gr
 oup of unique names (DN and Unique Identifier)' SUP top STRUCTURAL MUST ( uni
 queMember $ cn ) MAY ( businessCategory $ seeAlso $ owner $ ou $ o $ descript
 ion ) )
olcObjectClasses: {16}( 2.5.6.18 NAME 'userSecurityInformation' DESC 'RFC2256:
  a user security information' SUP top AUXILIARY MAY ( supportedAlgorithms ) )
olcObjectClasses: {17}( 2.5.6.16.2 NAME 'certificationAuthority-V2' SUP certif
 icationAuthority AUXILIARY MAY ( deltaRevocationList ) )
olcObjectClasses: {18}( 2.5.6.19 NAME 'cRLDistributionPoint' SUP top STRUCTURA
 L MUST ( cn ) MAY ( certificateRevocationList $ authorityRevocationList $ del
 taRevocationList ) )
olcObjectClasses: {19}( 2.5.6.20 NAME 'dmd' SUP top STRUCTURAL MUST ( dmdName 
 ) MAY ( userPassword $ searchGuide $ seeAlso $ businessCategory $ x121Address
  $ registeredAddress $ destinationIndicator $ preferredDeliveryMethod $ telex
 Number $ teletexTerminalIdentifier $ telephoneNumber $ internationaliSDNNumbe
 r $ facsimileTelephoneNumber $ street $ postOfficeBox $ postalCode $ postalAd
 dress $ physicalDeliveryOfficeName $ st $ l $ description ) )
olcObjectClasses: {20}( 2.5.6.21 NAME 'pkiUser' DESC 'RFC2587: a PKI user' SUP
  top AUXILIARY MAY userCertificate )
olcObjectClasses: {21}( 2.5.6.22 NAME 'pkiCA' DESC 'RFC2587: PKI certificate a
 uthority' SUP top AUXILIARY MAY ( authorityRevocationList $ certificateRevoca
 tionList $ cACertificate $ crossCertificatePair ) )
olcObjectClasses: {22}( 2.5.6.23 NAME 'deltaCRL' DESC 'RFC2587: PKI user' SUP 
 top AUXILIARY MAY deltaRevocationList )
olcObjectClasses: {23}( 1.3.6.1.4.1.250.3.15 NAME 'labeledURIObject' DESC 'RFC
 2079: object that contains the URI attribute type' MAY ( labeledURI ) SUP top
  AUXILIARY )
olcObjectClasses: {24}( 0.9.2342.19200300.100.4.19 NAME 'simpleSecurityObject'
  DESC 'RFC1274: simple security object' SUP top AUXILIARY MUST userPassword )
olcObjectClasses: {25}( 1.3.6.1.4.1.1466.344 NAME 'dcObject' DESC 'RFC2247: do
 main component object' SUP top AUXILIARY MUST dc )
olcObjectClasses: {26}( 1.3.6.1.1.3.1 NAME 'uidObject' DESC 'RFC2377: uid obje
 ct' SUP top AUXILIARY MUST uid )

# {1}cosine, schema, config
dn: cn={1}cosine,cn=schema,cn=config
objectClass: olcSchemaConfig
cn: {1}cosine
olcAttributeTypes: {0}( 0.9.2342.19200300.100.1.2 NAME 'textEncodedORAddress' 
 EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.
 1466.115.121.1.15{256} )
olcAttributeTypes: {1}( 0.9.2342.19200300.100.1.4 NAME 'info' DESC 'RFC1274: g
 eneral information' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch
  SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{2048} )
olcAttributeTypes: {2}( 0.9.2342.19200300.100.1.5 NAME ( 'drink' 'favouriteDri
 nk' ) DESC 'RFC1274: favorite drink' EQUALITY caseIgnoreMatch SUBSTR caseIgno
 reSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {3}( 0.9.2342.19200300.100.1.6 NAME 'roomNumber' DESC 'RFC1
 274: room number' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch S
 YNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {4}( 0.9.2342.19200300.100.1.7 NAME 'photo' DESC 'RFC1274: 
 photo (G3 fax)' SYNTAX 1.3.6.1.4.1.1466.115.121.1.23{25000} )
olcAttributeTypes: {5}( 0.9.2342.19200300.100.1.8 NAME 'userClass' DESC 'RFC12
 74: category of user' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMat
 ch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {6}( 0.9.2342.19200300.100.1.9 NAME 'host' DESC 'RFC1274: h
 ost computer' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTA
 X 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {7}( 0.9.2342.19200300.100.1.10 NAME 'manager' DESC 'RFC127
 4: DN of manager' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.4.1.1466.115
 .121.1.12 )
olcAttributeTypes: {8}( 0.9.2342.19200300.100.1.11 NAME 'documentIdentifier' D
 ESC 'RFC1274: unique identifier of document' EQUALITY caseIgnoreMatch SUBSTR 
 caseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {9}( 0.9.2342.19200300.100.1.12 NAME 'documentTitle' DESC '
 RFC1274: title of document' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstri
 ngsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {10}( 0.9.2342.19200300.100.1.13 NAME 'documentVersion' DES
 C 'RFC1274: version of document' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSu
 bstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {11}( 0.9.2342.19200300.100.1.14 NAME 'documentAuthor' DESC
  'RFC1274: DN of author of document' EQUALITY distinguishedNameMatch SYNTAX 1
 .3.6.1.4.1.1466.115.121.1.12 )
olcAttributeTypes: {12}( 0.9.2342.19200300.100.1.15 NAME 'documentLocation' DE
 SC 'RFC1274: location of document original' EQUALITY caseIgnoreMatch SUBSTR c
 aseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {13}( 0.9.2342.19200300.100.1.20 NAME ( 'homePhone' 'homeTe
 lephoneNumber' ) DESC 'RFC1274: home telephone number' EQUALITY telephoneNumb
 erMatch SUBSTR telephoneNumberSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121
 .1.50 )
olcAttributeTypes: {14}( 0.9.2342.19200300.100.1.21 NAME 'secretary' DESC 'RFC
 1274: DN of secretary' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.4.1.146
 6.115.121.1.12 )
olcAttributeTypes: {15}( 0.9.2342.19200300.100.1.22 NAME 'otherMailbox' SYNTAX
  1.3.6.1.4.1.1466.115.121.1.39 )
olcAttributeTypes: {16}( 0.9.2342.19200300.100.1.26 NAME 'aRecord' EQUALITY ca
 seIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
olcAttributeTypes: {17}( 0.9.2342.19200300.100.1.27 NAME 'mDRecord' EQUALITY c
 aseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
olcAttributeTypes: {18}( 0.9.2342.19200300.100.1.28 NAME 'mXRecord' EQUALITY c
 aseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
olcAttributeTypes: {19}( 0.9.2342.19200300.100.1.29 NAME 'nSRecord' EQUALITY c
 aseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
olcAttributeTypes: {20}( 0.9.2342.19200300.100.1.30 NAME 'sOARecord' EQUALITY 
 caseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
olcAttributeTypes: {21}( 0.9.2342.19200300.100.1.31 NAME 'cNAMERecord' EQUALIT
 Y caseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
olcAttributeTypes: {22}( 0.9.2342.19200300.100.1.38 NAME 'associatedName' DESC
  'RFC1274: DN of entry associated with domain' EQUALITY distinguishedNameMatc
 h SYNTAX 1.3.6.1.4.1.1466.115.121.1.12 )
olcAttributeTypes: {23}( 0.9.2342.19200300.100.1.39 NAME 'homePostalAddress' D
 ESC 'RFC1274: home postal address' EQUALITY caseIgnoreListMatch SUBSTR caseIg
 noreListSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.41 )
olcAttributeTypes: {24}( 0.9.2342.19200300.100.1.40 NAME 'personalTitle' DESC 
 'RFC1274: personal title' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstring
 sMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {25}( 0.9.2342.19200300.100.1.41 NAME ( 'mobile' 'mobileTel
 ephoneNumber' ) DESC 'RFC1274: mobile telephone number' EQUALITY telephoneNum
 berMatch SUBSTR telephoneNumberSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.12
 1.1.50 )
olcAttributeTypes: {26}( 0.9.2342.19200300.100.1.42 NAME ( 'pager' 'pagerTelep
 honeNumber' ) DESC 'RFC1274: pager telephone number' EQUALITY telephoneNumber
 Match SUBSTR telephoneNumberSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1
 .50 )
olcAttributeTypes: {27}( 0.9.2342.19200300.100.1.43 NAME ( 'co' 'friendlyCount
 ryName' ) DESC 'RFC1274: friendly country name' EQUALITY caseIgnoreMatch SUBS
 TR caseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 )
olcAttributeTypes: {28}( 0.9.2342.19200300.100.1.44 NAME 'uniqueIdentifier' DE
 SC 'RFC1274: unique identifer' EQUALITY caseIgnoreMatch SYNTAX 1.3.6.1.4.1.14
 66.115.121.1.15{256} )
olcAttributeTypes: {29}( 0.9.2342.19200300.100.1.45 NAME 'organizationalStatus
 ' DESC 'RFC1274: organizational status' EQUALITY caseIgnoreMatch SUBSTR caseI
 gnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {30}( 0.9.2342.19200300.100.1.46 NAME 'janetMailbox' DESC '
 RFC1274: Janet mailbox' EQUALITY caseIgnoreIA5Match SUBSTR caseIgnoreIA5Subst
 ringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.26{256} )
olcAttributeTypes: {31}( 0.9.2342.19200300.100.1.47 NAME 'mailPreferenceOption
 ' DESC 'RFC1274: mail preference option' SYNTAX 1.3.6.1.4.1.1466.115.121.1.27
  )
olcAttributeTypes: {32}( 0.9.2342.19200300.100.1.48 NAME 'buildingName' DESC '
 RFC1274: name of building' EQUALITY caseIgnoreMatch SUBSTR caseIgnoreSubstrin
 gsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{256} )
olcAttributeTypes: {33}( 0.9.2342.19200300.100.1.49 NAME 'dSAQuality' DESC 'RF
 C1274: DSA Quality' SYNTAX 1.3.6.1.4.1.1466.115.121.1.19 SINGLE-VALUE )
olcAttributeTypes: {34}( 0.9.2342.19200300.100.1.50 NAME 'singleLevelQuality' 
 DESC 'RFC1274: Single Level Quality' SYNTAX 1.3.6.1.4.1.1466.115.121.1.13 SIN
 GLE-VALUE )
olcAttributeTypes: {35}( 0.9.2342.19200300.100.1.51 NAME 'subtreeMinimumQualit
 y' DESC 'RFC1274: Subtree Mininum Quality' SYNTAX 1.3.6.1.4.1.1466.115.121.1.
 13 SINGLE-VALUE )
olcAttributeTypes: {36}( 0.9.2342.19200300.100.1.52 NAME 'subtreeMaximumQualit
 y' DESC 'RFC1274: Subtree Maximun Quality' SYNTAX 1.3.6.1.4.1.1466.115.121.1.
 13 SINGLE-VALUE )
olcAttributeTypes: {37}( 0.9.2342.19200300.100.1.53 NAME 'personalSignature' D
 ESC 'RFC1274: Personal Signature (G3 fax)' SYNTAX 1.3.6.1.4.1.1466.115.121.1.
 23 )
olcAttributeTypes: {38}( 0.9.2342.19200300.100.1.54 NAME 'dITRedirect' DESC 'R
 FC1274: DIT Redirect' EQUALITY distinguishedNameMatch SYNTAX 1.3.6.1.4.1.1466
 .115.121.1.12 )
olcAttributeTypes: {39}( 0.9.2342.19200300.100.1.55 NAME 'audio' DESC 'RFC1274
 : audio (u-law)' SYNTAX 1.3.6.1.4.1.1466.115.121.1.4{25000} )
olcAttributeTypes: {40}( 0.9.2342.19200300.100.1.56 NAME 'documentPublisher' D
 ESC 'RFC1274: publisher of document' EQUALITY caseIgnoreMatch SUBSTR caseIgno
 reSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 )
olcObjectClasses: {0}( 0.9.2342.19200300.100.4.4 NAME ( 'pilotPerson' 'newPilo
 tPerson' ) SUP person STRUCTURAL MAY ( userid $ textEncodedORAddress $ rfc822
 Mailbox $ favouriteDrink $ roomNumber $ userClass $ homeTelephoneNumber $ hom
 ePostalAddress $ secretary $ personalTitle $ preferredDeliveryMethod $ busine
 ssCategory $ janetMailbox $ otherMailbox $ mobileTelephoneNumber $ pagerTelep
 honeNumber $ organizationalStatus $ mailPreferenceOption $ personalSignature 
 ) )
olcObjectClasses: {1}( 0.9.2342.19200300.100.4.5 NAME 'account' SUP top STRUCT
 URAL MUST userid MAY ( description $ seeAlso $ localityName $ organizationNam
 e $ organizationalUnitName $ host ) )
olcObjectClasses: {2}( 0.9.2342.19200300.100.4.6 NAME 'document' SUP top STRUC
 TURAL MUST documentIdentifier MAY ( commonName $ description $ seeAlso $ loca
 lityName $ organizationName $ organizationalUnitName $ documentTitle $ docume
 ntVersion $ documentAuthor $ documentLocation $ documentPublisher ) )
olcObjectClasses: {3}( 0.9.2342.19200300.100.4.7 NAME 'room' SUP top STRUCTURA
 L MUST commonName MAY ( roomNumber $ description $ seeAlso $ telephoneNumber 
 ) )
olcObjectClasses: {4}( 0.9.2342.19200300.100.4.9 NAME 'documentSeries' SUP top
  STRUCTURAL MUST commonName MAY ( description $ seeAlso $ telephonenumber $ l
 ocalityName $ organizationName $ organizationalUnitName ) )
olcObjectClasses: {5}( 0.9.2342.19200300.100.4.13 NAME 'domain' SUP top STRUCT
 URAL MUST domainComponent MAY ( associatedName $ organizationName $ descripti
 on $ businessCategory $ seeAlso $ searchGuide $ userPassword $ localityName $
  stateOrProvinceName $ streetAddress $ physicalDeliveryOfficeName $ postalAdd
 ress $ postalCode $ postOfficeBox $ streetAddress $ facsimileTelephoneNumber 
 $ internationalISDNNumber $ telephoneNumber $ teletexTerminalIdentifier $ tel
 exNumber $ preferredDeliveryMethod $ destinationIndicator $ registeredAddress
  $ x121Address ) )
olcObjectClasses: {6}( 0.9.2342.19200300.100.4.14 NAME 'RFC822localPart' SUP d
 omain STRUCTURAL MAY ( commonName $ surname $ description $ seeAlso $ telepho
 neNumber $ physicalDeliveryOfficeName $ postalAddress $ postalCode $ postOffi
 ceBox $ streetAddress $ facsimileTelephoneNumber $ internationalISDNNumber $ 
 telephoneNumber $ teletexTerminalIdentifier $ telexNumber $ preferredDelivery
 Method $ destinationIndicator $ registeredAddress $ x121Address ) )
olcObjectClasses: {7}( 0.9.2342.19200300.100.4.15 NAME 'dNSDomain' SUP domain 
 STRUCTURAL MAY ( ARecord $ MDRecord $ MXRecord $ NSRecord $ SOARecord $ CNAME
 Record ) )
olcObjectClasses: {8}( 0.9.2342.19200300.100.4.17 NAME 'domainRelatedObject' D
 ESC 'RFC1274: an object related to an domain' SUP top AUXILIARY MUST associat
 edDomain )
olcObjectClasses: {9}( 0.9.2342.19200300.100.4.18 NAME 'friendlyCountry' SUP c
 ountry STRUCTURAL MUST friendlyCountryName )
olcObjectClasses: {10}( 0.9.2342.19200300.100.4.20 NAME 'pilotOrganization' SU
 P ( organization $ organizationalUnit ) STRUCTURAL MAY buildingName )
olcObjectClasses: {11}( 0.9.2342.19200300.100.4.21 NAME 'pilotDSA' SUP dsa STR
 UCTURAL MAY dSAQuality )
olcObjectClasses: {12}( 0.9.2342.19200300.100.4.22 NAME 'qualityLabelledData' 
 SUP top AUXILIARY MUST dsaQuality MAY ( subtreeMinimumQuality $ subtreeMaximu
 mQuality ) )

# {2}nis, schema, config
dn: cn={2}nis,cn=schema,cn=config
objectClass: olcSchemaConfig
cn: {2}nis
olcAttributeTypes: {0}( 1.3.6.1.1.1.1.2 NAME 'gecos' DESC 'The GECOS field; th
 e common name' EQUALITY caseIgnoreIA5Match SUBSTR caseIgnoreIA5SubstringsMatc
 h SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 SINGLE-VALUE )
olcAttributeTypes: {1}( 1.3.6.1.1.1.1.3 NAME 'homeDirectory' DESC 'The absolut
 e path to the home directory' EQUALITY caseExactIA5Match SYNTAX 1.3.6.1.4.1.1
 466.115.121.1.26 SINGLE-VALUE )
olcAttributeTypes: {2}( 1.3.6.1.1.1.1.4 NAME 'loginShell' DESC 'The path to th
 e login shell' EQUALITY caseExactIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.2
 6 SINGLE-VALUE )
olcAttributeTypes: {3}( 1.3.6.1.1.1.1.5 NAME 'shadowLastChange' EQUALITY integ
 erMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {4}( 1.3.6.1.1.1.1.6 NAME 'shadowMin' EQUALITY integerMatch
  SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {5}( 1.3.6.1.1.1.1.7 NAME 'shadowMax' EQUALITY integerMatch
  SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {6}( 1.3.6.1.1.1.1.8 NAME 'shadowWarning' EQUALITY integerM
 atch SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {7}( 1.3.6.1.1.1.1.9 NAME 'shadowInactive' EQUALITY integer
 Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {8}( 1.3.6.1.1.1.1.10 NAME 'shadowExpire' EQUALITY integerM
 atch SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {9}( 1.3.6.1.1.1.1.11 NAME 'shadowFlag' EQUALITY integerMat
 ch SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {10}( 1.3.6.1.1.1.1.12 NAME 'memberUid' EQUALITY caseExactI
 A5Match SUBSTR caseExactIA5SubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.
 26 )
olcAttributeTypes: {11}( 1.3.6.1.1.1.1.13 NAME 'memberNisNetgroup' EQUALITY ca
 seExactIA5Match SUBSTR caseExactIA5SubstringsMatch SYNTAX 1.3.6.1.4.1.1466.11
 5.121.1.26 )
olcAttributeTypes: {12}( 1.3.6.1.1.1.1.14 NAME 'nisNetgroupTriple' DESC 'Netgr
 oup triple' SYNTAX 1.3.6.1.1.1.0.0 )
olcAttributeTypes: {13}( 1.3.6.1.1.1.1.15 NAME 'ipServicePort' EQUALITY intege
 rMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {14}( 1.3.6.1.1.1.1.16 NAME 'ipServiceProtocol' SUP name )
olcAttributeTypes: {15}( 1.3.6.1.1.1.1.17 NAME 'ipProtocolNumber' EQUALITY int
 egerMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {16}( 1.3.6.1.1.1.1.18 NAME 'oncRpcNumber' EQUALITY integer
 Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.27 SINGLE-VALUE )
olcAttributeTypes: {17}( 1.3.6.1.1.1.1.19 NAME 'ipHostNumber' DESC 'IP address
 ' EQUALITY caseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26{128} )
olcAttributeTypes: {18}( 1.3.6.1.1.1.1.20 NAME 'ipNetworkNumber' DESC 'IP netw
 ork' EQUALITY caseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26{128} SI
 NGLE-VALUE )
olcAttributeTypes: {19}( 1.3.6.1.1.1.1.21 NAME 'ipNetmaskNumber' DESC 'IP netm
 ask' EQUALITY caseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26{128} SI
 NGLE-VALUE )
olcAttributeTypes: {20}( 1.3.6.1.1.1.1.22 NAME 'macAddress' DESC 'MAC address'
  EQUALITY caseIgnoreIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26{128} )
olcAttributeTypes: {21}( 1.3.6.1.1.1.1.23 NAME 'bootParameter' DESC 'rpc.bootp
 aramd parameter' SYNTAX 1.3.6.1.1.1.0.1 )
olcAttributeTypes: {22}( 1.3.6.1.1.1.1.24 NAME 'bootFile' DESC 'Boot image nam
 e' EQUALITY caseExactIA5Match SYNTAX 1.3.6.1.4.1.1466.115.121.1.26 )
olcAttributeTypes: {23}( 1.3.6.1.1.1.1.26 NAME 'nisMapName' SUP name )
olcAttributeTypes: {24}( 1.3.6.1.1.1.1.27 NAME 'nisMapEntry' EQUALITY caseExac
 tIA5Match SUBSTR caseExactIA5SubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.
 1.26{1024} SINGLE-VALUE )
olcObjectClasses: {0}( 1.3.6.1.1.1.2.0 NAME 'posixAccount' DESC 'Abstraction o
 f an account with POSIX attributes' SUP top AUXILIARY MUST ( cn $ uid $ uidNu
 mber $ gidNumber $ homeDirectory ) MAY ( userPassword $ loginShell $ gecos $ 
 description ) )
olcObjectClasses: {1}( 1.3.6.1.1.1.2.1 NAME 'shadowAccount' DESC 'Additional a
 ttributes for shadow passwords' SUP top AUXILIARY MUST uid MAY ( userPassword
  $ shadowLastChange $ shadowMin $ shadowMax $ shadowWarning $ shadowInactive 
 $ shadowExpire $ shadowFlag $ description ) )
olcObjectClasses: {2}( 1.3.6.1.1.1.2.2 NAME 'posixGroup' DESC 'Abstraction of 
 a group of accounts' SUP top STRUCTURAL MUST ( cn $ gidNumber ) MAY ( userPas
 sword $ memberUid $ description ) )
olcObjectClasses: {3}( 1.3.6.1.1.1.2.3 NAME 'ipService' DESC 'Abstraction an I
 nternet Protocol service' SUP top STRUCTURAL MUST ( cn $ ipServicePort $ ipSe
 rviceProtocol ) MAY description )
olcObjectClasses: {4}( 1.3.6.1.1.1.2.4 NAME 'ipProtocol' DESC 'Abstraction of 
 an IP protocol' SUP top STRUCTURAL MUST ( cn $ ipProtocolNumber $ description
  ) MAY description )
olcObjectClasses: {5}( 1.3.6.1.1.1.2.5 NAME 'oncRpc' DESC 'Abstraction of an O
 NC/RPC binding' SUP top STRUCTURAL MUST ( cn $ oncRpcNumber $ description ) M
 AY description )
olcObjectClasses: {6}( 1.3.6.1.1.1.2.6 NAME 'ipHost' DESC 'Abstraction of a ho
 st, an IP device' SUP top AUXILIARY MUST ( cn $ ipHostNumber ) MAY ( l $ desc
 ription $ manager ) )
olcObjectClasses: {7}( 1.3.6.1.1.1.2.7 NAME 'ipNetwork' DESC 'Abstraction of a
 n IP network' SUP top STRUCTURAL MUST ( cn $ ipNetworkNumber ) MAY ( ipNetmas
 kNumber $ l $ description $ manager ) )
olcObjectClasses: {8}( 1.3.6.1.1.1.2.8 NAME 'nisNetgroup' DESC 'Abstraction of
  a netgroup' SUP top STRUCTURAL MUST cn MAY ( nisNetgroupTriple $ memberNisNe
 tgroup $ description ) )
olcObjectClasses: {9}( 1.3.6.1.1.1.2.9 NAME 'nisMap' DESC 'A generic abstracti
 on of a NIS map' SUP top STRUCTURAL MUST nisMapName MAY description )
olcObjectClasses: {10}( 1.3.6.1.1.1.2.10 NAME 'nisObject' DESC 'An entry in a 
 NIS map' SUP top STRUCTURAL MUST ( cn $ nisMapEntry $ nisMapName ) MAY descri
 ption )
olcObjectClasses: {11}( 1.3.6.1.1.1.2.11 NAME 'ieee802Device' DESC 'A device w
 ith a MAC address' SUP top AUXILIARY MAY macAddress )
olcObjectClasses: {12}( 1.3.6.1.1.1.2.12 NAME 'bootableDevice' DESC 'A device 
 with boot parameters' SUP top AUXILIARY MAY ( bootFile $ bootParameter ) )

# {3}inetorgperson, schema, config
dn: cn={3}inetorgperson,cn=schema,cn=config
objectClass: olcSchemaConfig
cn: {3}inetorgperson
olcAttributeTypes: {0}( 2.16.840.1.113730.3.1.1 NAME 'carLicense' DESC 'RFC279
 8: vehicle license or registration plate' EQUALITY caseIgnoreMatch SUBSTR cas
 eIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 )
olcAttributeTypes: {1}( 2.16.840.1.113730.3.1.2 NAME 'departmentNumber' DESC '
 RFC2798: identifies a department within an organization' EQUALITY caseIgnoreM
 atch SUBSTR caseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 )
olcAttributeTypes: {2}( 2.16.840.1.113730.3.1.241 NAME 'displayName' DESC 'RFC
 2798: preferred name to be used when displaying entries' EQUALITY caseIgnoreM
 atch SUBSTR caseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 SI
 NGLE-VALUE )
olcAttributeTypes: {3}( 2.16.840.1.113730.3.1.3 NAME 'employeeNumber' DESC 'RF
 C2798: numerically identifies an employee within an organization' EQUALITY ca
 seIgnoreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.12
 1.1.15 SINGLE-VALUE )
olcAttributeTypes: {4}( 2.16.840.1.113730.3.1.4 NAME 'employeeType' DESC 'RFC2
 798: type of employment for a person' EQUALITY caseIgnoreMatch SUBSTR caseIgn
 oreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.15 )
olcAttributeTypes: {5}( 0.9.2342.19200300.100.1.60 NAME 'jpegPhoto' DESC 'RFC2
 798: a JPEG image' SYNTAX 1.3.6.1.4.1.1466.115.121.1.28 )
olcAttributeTypes: {6}( 2.16.840.1.113730.3.1.39 NAME 'preferredLanguage' DESC
  'RFC2798: preferred written or spoken language for a person' EQUALITY caseIg
 noreMatch SUBSTR caseIgnoreSubstringsMatch SYNTAX 1.3.6.1.4.1.1466.115.121.1.
 15 SINGLE-VALUE )
olcAttributeTypes: {7}( 2.16.840.1.113730.3.1.40 NAME 'userSMIMECertificate' D
 ESC 'RFC2798: PKCS#7 SignedData used to support S/MIME' SYNTAX 1.3.6.1.4.1.14
 66.115.121.1.5 )
olcAttributeTypes: {8}( 2.16.840.1.113730.3.1.216 NAME 'userPKCS12' DESC 'RFC2
 798: personal identity information, a PKCS #12 PFX' SYNTAX 1.3.6.1.4.1.1466.1
 15.121.1.5 )
olcObjectClasses: {0}( 2.16.840.1.113730.3.2.2 NAME 'inetOrgPerson' DESC 'RFC2
 798: Internet Organizational Person' SUP organizationalPerson STRUCTURAL MAY 
 ( audio $ businessCategory $ carLicense $ departmentNumber $ displayName $ em
 ployeeNumber $ employeeType $ givenName $ homePhone $ homePostalAddress $ ini
 tials $ jpegPhoto $ labeledURI $ mail $ manager $ mobile $ o $ pager $ photo 
 $ roomNumber $ secretary $ uid $ userCertificate $ x500uniqueIdentifier $ pre
 ferredLanguage $ userSMIMECertificate $ userPKCS12 ) )

# {-1}frontend, config
dn: olcDatabase={-1}frontend,cn=config
objectClass: olcDatabaseConfig
objectClass: olcFrontendConfig
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
root@sdi04a:~/ldap_config# 
```