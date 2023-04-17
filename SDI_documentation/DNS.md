# DNS

## How to setup a BIND Authoritative DNS server

In order to set up an [Authoritative DNS](https://support.cpanel.net/hc/en-us/articles/360056527174-Authoritative-VS-Non-Authoritative-DNS-Servers) Server you have to install "bind9" via the the following command:
`$ aptitude install bind9`

Output should look like this:

```sh
root@sdi04a:~# aptitude install bind9
Die folgenden NEUEN Pakete werden zusätzlich installiert:
  bind9 bind9-utils{a} dns-root-data{a} python3-ply{a}
0 Pakete aktualisiert, 4 zusätzlich installiert, 0 werden entfernt und 0 nicht aktualisiert.
1.002 kB an Archiven müssen heruntergeladen werden. Nach dem Entpacken werden 2.370 kB zusätzlich belegt sein.
Möchten Sie fortsetzen? [Y/n/?] y
...
```

### Manage BIND Services

Bind9 is managed by [systemd](https://freedesktop.org/wiki/Software/systemd/). To run it therefore, you have to use the `systemctl` command.

The full command looks like this: `$ systemctl start named`.

To enable autostart run: `$ systemctl enable named`.

To check if it is running properly, run
`$ systemctl status named`.

To restart BIND use `$ systemctl restart named`

The status check should return something like this:

```bash
root@sdi04a:~# systemctl status named
● named.service - BIND Domain Name Server
    Loaded: loaded (/lib/systemd/system/named.service; enabled; vendor preset:>
    Active: active (running) since Mon 2023-04-03 14:32:24 CEST; 1h 26min ago
      Docs: man:named(8)
  Main PID: 2629 (named)
     Tasks: 5 (limit: 619009)
    Memory: 1.0G
       CPU: 1.339s
    CGroup: /system.slice/named.service
            └─2629 /usr/sbin/named -f -u bind
...
```

### Configure BIND Services

Bind DNS servers store all their configuration files inside the /etc/bind directory. You can list all of them with the following command:
`$ ls -l /etc/bind/`

One of the first things you should do is to edit the file /etc/bind/named.conf.options and add forwarders. These forwarders are other DNS servers to which the query will be redirected to if your own DNS server is unable to resolve them.

Here is an example on how to edit the file:

```sh
forwarders {
        1.1.1.1; // Cloudflare
        8.8.8.8; // Google
};
```

In our example we use the DNS Server of Google and Cloudfare which are reachable under the address 1.1.1.1 and 8.8.8.8 respectively.

Second, you should edit the /etc/bind/named.conf.local file to define the forward and reverse lookup zones for your domain. The former is responsible for turning names into IPs, and the latter is responsible for doing the opposite.

Here is an example on how to edit the file:

```
zone "sdi4.mi.hdm-stuttgart.de"
{
        type master;
        file "/etc/bind/Zones/forward.sdi4.mi.hdm-stuttgart.de";
};

zone "75.62.141.in-addr.arpa"{
      type master;
      file "/etc/bind/Zones/reverse.sdi4.mi.hdm-stuttgart.de
};

```

Now you need to create the zone files. We do this by copying the templates:

```sh
$ cp /etc/bind/db.127 /etc/bind/Zones/reverse.mi.hdm-stuttgart.de
$ cp /etc/bind/db.local /etc/bind/Zones/forward.mi.hdm-stuttgart.de
```

#### Forward zone

The unedited template file for forward-zone looks like this:

```sh
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL~
;
@       IN      NS      localhost.
@       IN      A       127.0.0.1
@       IN      AAAA    ::1
```

First we need to specify the SOA (start-of-Authority).
For this, we need to replace the `localhost.` with our primary name server : `ns1.sdi4.mi.hdm-stuttgart.de.`. **Take care to include the dot at the end.**

You can freely choose the name of the name server, we decided to name our server "ns1".

1. We need to edit the NS (nameserver) record to match our name server. So we replace `localhost.` with our primary nameserver address: `ns1.sdi4.mi.hdm-stuttgart.de.`.
2. We need to define an address-record to be able to get the coresponding IP address to our nameserver `ns1`. To achieve this, we need to add this line: `ns1  IN  A   141.62.75.104`
3. Now we need to specify our domain names to their coresponding IP addresses. For us its:

```
sdi04a.sdi4.mi.hdm-stuttgart.de IN      A       141.62.75.104
sdi04b.sdi4.mi.hdm-stuutagrt.de IN      A       141.62.75.118
```

Finally it should look like this:

```
$TTL    604800
@       IN      SOA     ns1.sdi4.mi.hdm-stuttgart.de. root.ns1.sdi4.mi.hdm-stuttgart.de. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.sdi4.mi.hdm-stuttgart.de.
ns1     IN      A       141.62.75.104
sdi04a.sdi4.mi.hdm-stuttgart.de IN      A       141.62.75.104
sdi04b.sdi4.mi.hdm-stuutagrt.de IN      A       141.62.75.118
@       IN      A       127.0.0.1
@       IN      AAAA    ::1
```

#### Reverse zone

The unedited template file for reverse-zone looks like this:

```
$TTL    604800
@       IN      SOA     localhost. root.localhost. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      localhost.
1.0.0   IN      PTR     localhost.
```

The changes we need to make are mostly the same as the ones above.

First we need to specify the SOA (start-of-Authority).
For this, we need to replace the `localhost.` with our primary name server : `ns1.sdi4.mi.hdm-stuttgart.de.`. **Take care to include the dot at the end.**

1. We need to edit the NS (nameserver) record to match our name server. So we replace `localhost.` with our primary nameserver address: `ns1.sdi4.mi.hdm-stuttgart.de.`

2. Now we need to specify our IP adresses to their coresponding Domain names. For us its:

```
141.62.75.104   IN      PTR     sdi04a.mi.hdm-stuttgart.de.
141.62.75.118   IN      PTR     sdi04b.mi.hdm-stuttgart.de.
```

3. We need to define an address-record to be able to get the coresponding IP address to our nameserver `ns1`. To achieve this, we need to add this line: `ns1  IN  A   141.62.75.104`

Finally it should look like this:

```
@       IN      SOA     ns1.sdi4.mi.hdm-stuttgart.de. root.ns1.sdi4.mi.hdm-stuttgart.de. (
                              1         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      ns1.sdi4.mi.hdm-stuttgart.de.
141.62.75.104   IN      PTR     sdi04a.mi.hdm-stuttgart.de.
141.62.75.118   IN      PTR     sdi04b.mi.hdm-stuttgart.de.
ns1     IN      A       141.62.75.104
```

#### Edit `resolve.conf`

Last but not least we need to edit the resolve.conf. It looks like this after editing:

```sh
# --- BEGIN PVE ---
search sdi4.mi.hdm-stuttgart.de
ns1 141.62.75.104
# --- END PVE ---
```

After u are done with everything **Restart** Bind with `$ systemctl restart named`

Now we check forward lookup zone file for any syntax error with: `named-checkzone forward.sdi04a.mi.hdm-stuttgart forward.sdi04a.mi.hdm-stuttgart.com`

The result should look like

```
zone forward.exampledomain/IN: loaded serial 2
OK
```

Now we check reverse lookup zone file for any syntax error with: `named-checkzone reverse.sdi04a.mi.hdm-stuttgart reverse.sdi04a.mi.hdm-stuttgart.com`

The result should look like:

```
zone reverse.exampledomain/IN: loaded serial 1
OK
```

## Testing BIND DNS server functuality (aka "does it work?")

The BIND server is now fully configured and installed. We use the dig tool to test:

`$ dig ns1.sdi4.mi.hdm-stuttgart.de`

You should see the following output:

```
; <<>> DiG 9.16.37-Debian <<>> ns1.sdi4.mi.hdm-stuttgart.de
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 26502
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 0d79abfe7a5dbe0e01000000643d542464f42d119ffe9a06 (good)
;; QUESTION SECTION:
;ns1.sdi4.mi.hdm-stuttgart.de.	IN	A

;; ANSWER SECTION:
ns1.sdi4.mi.hdm-stuttgart.de. 604800 IN	A	141.62.75.104

;; Query time: 0 msec
;; SERVER: ::1#53(::1)
;; WHEN: Mon Apr 17 16:13:56 CEST 2023
;; MSG SIZE  rcvd: 101
```

Now, run this dig command: `$ dig -x 141.62.75.104` to perform the reverse lookup query. The output should look like this:

```
; <<>> DiG 9.16.37-Debian <<>> -x 141.62.75.104
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43388
;; flags: qr aa rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 6993afbdb01fd06101000000643d555005cc24b385b64798 (good)
;; QUESTION SECTION:
;104.75.62.141.in-addr.arpa.	IN	PTR

;; AUTHORITY SECTION:
75.62.141.in-addr.arpa.	604800	IN	SOA	ns1.sdi4.mi.hdm-stuttgart.de. root.ns1.sdi4.mi.hdm-stuttgart.de. 1 604800 86400 2419200 604800

;; Query time: 0 msec
;; SERVER: ::1#53(::1)
;; WHEN: Mon Apr 17 16:18:56 CEST 2023
;; MSG SIZE  rcvd: 152
```

## Mail exchange record

The configuration of a mail record is very similar to the configuration of the the name. All you need to do is to add this line to the forward zone file: `@ IN MX 10 mx1.hdm-stuttgart.de`

To test if everything works fine run this command: `$ dig @141.62.75.104 mx1.hdm-stuttgart.de`

The output should look like this:

```
; <<>> DiG 9.16.37-Debian <<>> @141.62.75.104 mx1.hdm-stuttgart.de
; (1 server found)
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5776
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: e1e6457b959cf6bd01000000643d59e1b8fa18809fa4dd05 (good)
;; QUESTION SECTION:
;mx1.hdm-stuttgart.de.		IN	A

;; ANSWER SECTION:
mx1.hdm-stuttgart.de.	3600	IN	A	141.62.1.22

;; Query time: 31 msec
;; SERVER: 141.62.75.104#53(141.62.75.104)
;; WHEN: Mon Apr 17 16:38:25 CEST 2023
;; MSG SIZE  rcvd: 93
```