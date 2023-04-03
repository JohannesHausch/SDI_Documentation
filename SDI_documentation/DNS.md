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

Now you need to create the zone file. We do this by copying the template:

```sh
cp /etc/bind/db.empty /etc/bind/Zones/db.mi.hdm-stuttgart.de
```
