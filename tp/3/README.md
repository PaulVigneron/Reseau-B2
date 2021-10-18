# TP3 : Progressons vers le réseau d'infrastructure

## I. (mini)Architecture réseau

* **🌞 Vous me rendrez un 🗃️ tableau des réseaux 🗃️ qui rend compte des adresses choisies**

| Nom du réseau | Adresse du réseau | Masque        | Nombre de clients possibles | Adresse passerelle | [Adresse broadcast](../../cours/lexique/README.md#adresse-de-diffusion-ou-broadcast-address) |
|---------------|-------------------|---------------|-----------------------------|--------------------|----------------------------------------------------------------------------------------------|
| `client1`     | `10.3.0.0`        | `255.255.255.192` | `62`                           | `10.3.0.2`         | `10.3.0.63`                                                                                   |
| `server1`     | `10.3.0.64`        | `255.255.255.128` | `126`                           | `10.3.0.66`         | `10.3.1.190`                                                                                   |
| `server2`     | `10.3.0.192`        | `255.255.255.240` | `14`                           | `10.3.0.194`         | `10.3.2.207`                                                                                   |

* **🌞 Vous pouvez d'ores-et-déjà créer le routeur. Pour celui-ci, vous me prouverez que :**

* Il a bien une IP dans les 3 réseaux, l'IP que vous avez choisie comme IP de passerelle

```
[paul@routeur ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:e5:55:92 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 brd 10.0.2.255 scope global dynamic noprefixroute enp0s3
       valid_lft 86019sec preferred_lft 86019sec
    inet6 fe80::a00:27ff:fee5:5592/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:b3:c9:e1 brd ff:ff:ff:ff:ff:ff
    inet 10.3.0.10/26 brd 10.3.0.63 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:feb3:c9e1/64 scope link
       valid_lft forever preferred_lft forever
4: enp0s9: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:6b:ca:8f brd ff:ff:ff:ff:ff:ff
    inet 10.3.0.66/25 brd 10.3.0.127 scope global noprefixroute enp0s9
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe6b:ca8f/64 scope link
       valid_lft forever preferred_lft forever
5: enp0s10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:e0:5f:04 brd ff:ff:ff:ff:ff:ff
    inet 10.3.0.194/28 brd 10.3.0.207 scope global noprefixroute enp0s10
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fee0:5f04/64 scope link
       valid_lft forever preferred_lft forever
```

* Il a un accès internet

```
[paul@routeur ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=114 time=25.1 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=114 time=24.1 ms
^C
--- 8.8.8.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 24.127/24.629/25.132/0.526 ms
```

* Il a de la résolution de noms

```
[paul@routeur ~]$ [paul@routeur ~]$ ping google.com
PING google.com (216.58.214.78) 56(84) bytes of data.
64 bytes from fra15s10-in-f14.1e100.net (216.58.214.78): icmp_seq=1 ttl=114 time=25.10 ms
64 bytes from fra15s10-in-f14.1e100.net (216.58.214.78): icmp_seq=2 ttl=114 time=118 ms
^C
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 25.973/71.850/117.728/45.878 ms
```

* Il porte le nom router.tp3

![](https://i.imgur.com/fpght2Q.png)



* **🌞 Vous remplirez aussi au fur et à mesure que vous avancez dans le TP, au fur et à mesure que vous créez des machines, le 🗃️ tableau d'adressage 🗃**

| Nom machine  | Adresse IP `client1` | Adresse IP `server1` | Adresse IP `server2` | Adresse de passerelle |
|--------------|----------------------|----------------------|----------------------|-----------------------|
| `router.tp3` | `10.3.0.10/26`         | `10.3.0.10/25`         | `10.3.0.10/28`         | Carte NAT             |
| `dhcp.client.tp3`         | `10.3.0.3`                 | `no`                  | `no`                  | `10.3.0.2/26`          |
| `marcel.client.tp3` | `dhcp`         | `no`         | `no`         | `10.3.0.2/26`             |    
| `dns1.serveur1.tp3`          | `no`                  | `10.3.0.67/25`                  | `no`                  | `10.3.0.66/25`          |
| `johnny.client.tp3` | `dhcp`         | `no`         | `no`         | `10.3.0.2/26`             |
| `web1.serveur2.tp3`          | `no`                  | `no`                  | `10.3.0.195/28`                  | `10.3.0.194/28`          |
| `nfs1.serveur2.tp3` | `no`         | `no`         | `10.3.0.196/28`         | `10.3.0.194/28`             |

## 1. Serveur DHCP

**🌞 Mettre en place une machine qui fera office de serveur DHCP dans le réseau client1**

* Donner une IP aux machines clients qui le demande

```
default-lease-time 900;
max-lease-time 10800;
ddns-update-style none;
authoritative;
subnet 10.3.0.0 netmask 255.255.255.192 {
  range 10.3.0.4 10.3.0.62;
  option routers 10.3.0.2;
  option subnet-mask 255.255.255.192;
  option domain-name-servers 8.8.8.8;

}
```


**🌞 Mettre en place un client dans le réseau client1**

* La machine récupérera une IP dynamiquement grâce au serveur DHC

* Ainsi que sa passerelle et une adresse d'un DNS utilisable

**🌞 Depuis marcel.client1.tp3**

* Prouver qu'il a un accès internet + résolution de noms, avec des infos récupérées par votre DHCP

`sudo dhclient`

```
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:30:e0:45 brd ff:ff:ff:ff:ff:ff
    inet 10.3.0.5/26 brd 10.3.0.63 scope global dynamic noprefixroute enp0s8
       valid_lft 543sec preferred_lft 543sec
    inet6 fe80::a00:27ff:fe30:e045/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```

* A l'aide de la commande traceroute, prouver que marcel.client1.tp3 passe par router.tp3 pour sortir de son réseau

```
[paul@marcel ~]$ sudo traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  _gateway (10.3.0.10)  2.040 ms  2.045 ms  1.948 ms
```

## 2. Serveur DNS

**🌞 Mettre en place une machine qui fera office de serveur DNS**

* Il faudra lui ajouter un serveur DNS public connu, afin qu'il soit capable de résoudre des noms publics comme google.com

```
[paul@dns1 ~]$ sudo nano /etc/resolv.conf
```

```
# Generated by NetworkManager
nameserver 1.1.1.1
```

```
[paul@dns1 ~]$ curl google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>301 Moved</TITLE></HEAD><BODY>
<H1>301 Moved</H1>
The document has moved
<A HREF="http://www.google.com/">here</A>.
</BODY></HTML>
```

* Comme pour le DHCP, on part sur "rocky linux dns server" on Google pour les détails de l'install du serveur DNS.

`sudo nano /etc/named.conf`

```
zone "server1.tp3" IN {
        type master;
        file "server1.tp3.forward";
        allow-update { none; };
};
zone "server2.tp3" IN {
        type master;
        file "server2.tp3.forward";
        allow-update { none; };
};
zone "client1.tp3" IN {
        type master;
        file "client1.tp3.forward";
        allow-update { none; };
};
```

```
sudo nano /var/named/client1.tp3.forward
```

```
$TTL 86400
@   IN  SOA     dns1.server1.tp3. root.server1.tp3. (
         2021080804  ;Serial
         3600        ;Refresh
         1800        ;Retry
         604800      ;Expire
         86400       ;Minimum TTL
)
        ; Set your Name Servers here
@         IN  NS      dns1.server1.tp3.
         ; define Name Server's IP address
@         IN  A       10.3.0.67

; Set each IP address of a hostname. Sample A records.
dhcp           IN  A       10.3.0.3
router  IN A    10.3.0.10
```

`sudo nano /var/named/server1.tp3.forward`

```
$TTL 86400
@   IN  SOA     dns1.server1.tp3. root.server1.tp3. (
         2021080804  ;Serial
         3600        ;Refresh
         1800        ;Retry
         604800      ;Expire
         86400       ;Minimum TTL
)
        ; Set your Name Servers here
@         IN  NS      dns1.server1.tp3.
         ; define Name Server's IP address
@         IN  A       10.3.0.67

; Set each IP address of a hostname. Sample A records.
dns1           IN  A       10.3.0.67
router         IN  A       10.3.0.66
```

`sudo nano /var/named/server2.tp3.forward`

```
$TTL 86400
@   IN  SOA     dns1.server2.tp3. root.server2.tp3. (
         2021080804  ;Serial
         3600        ;Refresh
         1800        ;Retry
         604800      ;Expire
         86400       ;Minimum TTL
)
        ; Set your Name Servers here
@         IN  NS      dns1.server1.tp3.
         ; define Name Server's IP address
@         IN  A       10.3.0.67

; Set each IP address of a hostname. Sample A records.
router   IN A   10.3.0.194
```

```
sudo chmod 644 /var/named/client1.tp3.forward
sudo chmod 644 /var/named/server1.tp3.forward
sudo chmod 644 /var/named/server2.tp3.forward
```


`sudo firewall-cmd --add-service=dns --permanent`
`sudo firewall-cmd --reload`

`sudo nano /etc/resolv.conf`

```
[paul@dns1 ~]$ sudo cat /etc/resolv.conf
# Generated by NetworkManager
nameserver 10.3.0.67
```

`sudo systemctl enable --now named.service`

`systemctl status named`

```
[paul@dns1 ~]$ systemctl status named
● named.service - Berkeley Internet Name Domain (DNS)
   Loaded: loaded (/usr/lib/systemd/system/named.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2021-09-30 17:01:52 CEST; 9min ago
  Process: 2430 ExecStart=/usr/sbin/named -u named -c ${NAMEDCONF} $OPTIONS (code=exited, status=0/SUCCESS)
  Process: 2427 ExecStartPre=/bin/bash -c if [ ! "$DISABLE_ZONE_CHECKING" == "yes" ]; then /usr/sbin/named-checkconf -z "$NAMEDCON>
 Main PID: 2432 (named)
    Tasks: 4 (limit: 4947)
   Memory: 58.4M
   CGroup: /system.slice/named.service
           └─2432 /usr/sbin/named -u named -c /etc/named.conf
```

**🌞 Tester le DNS depuis marcel.client1.tp3**

```
[paul@marcel ~]$ dig dhcp.client1.tp3

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> dhcp.client1.tp3
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 20546
;; flags: qr rd ra ad; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;dhcp.client1.tp3.              IN      A

;; AUTHORITY SECTION:
.                       86392   IN      SOA     a.root-servers.net. nstld.verisign-grs.com. 2021093000 1800 900 604800 86400

;; Query time: 23 msec
;; SERVER: 8.8.8.8#53(8.8.8.8)
;; WHEN: Thu Sep 30 17:13:03 CEST 2021
;; MSG SIZE  rcvd: 120
```

**🌞 Configurez l'utilisation du serveur DNS sur TOUS vos noeuds**

```
[paul@dhcp ~]$ sudo cat /etc/dhcp/dhcpd.conf
[sudo] password for paul:
#
# DHCP Server Configuration file.
#   see /usr/share/doc/dhcp-server/dhcpd.conf.example
#   see dhcpd.conf(5) man page
#
default-lease-time 900;
max-lease-time 10800;
ddns-update-style none;
authoritative;
subnet 10.3.0.0 netmask 255.255.255.192 {
  range 10.3.0.4 10.3.0.62;
  option routers 10.3.0.10;
  option subnet-mask 255.255.255.192;
  option domain-name-servers 10.3.0.67;

}
```

## 3. Get deeper

A. DNS forwarder

**🌞 Affiner la configuration du DNS**

```
[paul@dns1 ~]$ sudo cat /etc/named.conf
[sudo] password for paul:
//
// named.conf
//
// Provided by Red Hat bind package to configure the ISC BIND named(8) DNS
// server as a caching only nameserver (as a localhost DNS resolver only).
//
// See /usr/share/doc/bind*/sample/ for example named configuration files.
//

options {
        listen-on port 53 { any; };
        listen-on-v6 port 53 { any; };
        directory       "/var/named";
        dump-file       "/var/named/data/cache_dump.db";
        statistics-file "/var/named/data/named_stats.txt";
        memstatistics-file "/var/named/data/named_mem_stats.txt";
        secroots-file   "/var/named/data/named.secroots";
        recursing-file  "/var/named/data/named.recursing";
        allow-query     { any; };

        /*
         - If you are building an AUTHORITATIVE DNS server, do NOT enable recursion.
         - If you are building a RECURSIVE (caching) DNS server, you need to enable
           recursion.
         - If your recursive DNS server has a public IP address, you MUST enable access
           control to limit queries to your legitimate users. Failing to do so will
           cause your server to become part of large scale DNS amplification
           attacks. Implementing BCP38 within your network would greatly
           reduce such attack surface
        */
        recursion yes;
```

**🌞 Test !**

```
[paul@marcel ~]$ dig google.com

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 6611
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 4, ADDITIONAL: 9

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: 14077ff055633578ba3ec2366154a2e9990af988d8d39a4a (good)
;; QUESTION SECTION:
;google.com.                    IN      A

;; ANSWER SECTION:
google.com.             300     IN      A       142.250.179.78

;; AUTHORITY SECTION:
google.com.             172799  IN      NS      ns2.google.com.
google.com.             172799  IN      NS      ns1.google.com.
google.com.             172799  IN      NS      ns3.google.com.
google.com.             172799  IN      NS      ns4.google.com.

;; ADDITIONAL SECTION:
ns2.google.com.         172799  IN      A       216.239.34.10
ns1.google.com.         172799  IN      A       216.239.32.10
ns3.google.com.         172799  IN      A       216.239.36.10
ns4.google.com.         172799  IN      A       216.239.38.10
ns2.google.com.         172799  IN      AAAA    2001:4860:4802:34::a
ns1.google.com.         172799  IN      AAAA    2001:4860:4802:32::a
ns3.google.com.         172799  IN      AAAA    2001:4860:4802:36::a
ns4.google.com.         172799  IN      AAAA    2001:4860:4802:38::a

;; Query time: 532 msec
;; SERVER: 10.3.0.67#53(10.3.0.67)
;; WHEN: Wed Sep 29 19:31:21 CEST 2021
;; MSG SIZE  rcvd: 331
```

### B. On revient sur la conf du DHCP

🌞 Affiner la configuration du DHCP

```
[paul@johny ~]$ ip a
[...]
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:3c:bb:39 brd ff:ff:ff:ff:ff:ff
    inet 10.3.0.4/26 brd 10.3.0.63 scope global dynamic noprefixroute enp0s8
       valid_lft 718sec preferred_lft 718sec
    inet6 fe80::a00:27ff:fe3c:bb39/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
```

[paul@johny ~]$ [paul@johny ~]$ sudo cat /etc/resolv.conf
# Generated by NetworkManager
search client1.tp3
nameserver 10.3.0.67
```

**🌞 Setup d'une nouvelle machine, qui sera un serveur Web, une belle appli pour nos clients**

```
[paul@web1 ~]$ sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
   Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; vendor preset: disabled)
   Active: active (running) since Tue 2021-10-05 16:28:14 CEST; 24min ago
  Process: 11002 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
  Process: 11001 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
  Process: 10996 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
 Main PID: 11005 (nginx)
    Tasks: 2 (limit: 4947)
   Memory: 3.7M
   CGroup: /system.slice/nginx.service
           ├─11005 nginx: master process /usr/sbin/nginx
           └─11006 nginx: worker process
```

**🌞 Test test test et re-test**

* Testez que votre serveur web est accessible depuis marcel.client1.tp3

```
[paul@marcel ~]$ sudo curl 10.3.0.195
[sudo] password for paul:
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
  <head>
    <title>Test Page for the Nginx HTTP Server on Rocky Linux</title>
    <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    <style type="text/css">
      /*<![CDATA[*/
      body {
        background-color: #fff;
        color: #000;
        font-size: 0.9em;
        font-family: sans-serif, helvetica;
        margin: 0;
        padding: 0;
      }
      :link {
        color: #c00;
      }
      :visited {
        color: #c00;
      }
      a:hover {
        color: #f50;
      }
```

## 2. Partage de fichiers

### B. Le setup wola

**🌞 Setup d'une nouvelle machine, qui sera un serveur NFS**

```
[paul@nfs1 home]$ sudo systemctl status rpcbind
● rpcbind.service - RPC Bind
   Loaded: loaded (/usr/lib/systemd/system/rpcbind.service; enabled; vendor preset: enabled)
   Active: active (running) since Tue 2021-10-05 17:35:23 CEST; 5min ago
     Docs: man:rpcbind(8)
 Main PID: 2479 (rpcbind)
    Tasks: 1 (limit: 4947)
   Memory: 1.6M
   CGroup: /system.slice/rpcbind.service
           └─2479 /usr/bin/rpcbind -w -f
```

**🌞 Configuration du client NFS**

* Effectuez de la configuration sur web1.server2.tp3 pour qu'il accède au partage NFS


`[paul@web1 ~]$ sudo mount -t nfs nfs1.server2.tp3:/home/srv/nfs_share /srv/nfs/`

```
[paul@web1 ~]$ df -hT /srv/nfs/
Filesystem                           Type  Size  Used Avail Use% Mounted on
nfs1.server2.tp3:/home/srv/nfs_share nfs4  6.2G  2.1G  4.2G  34% /srv/nfs
```

**🌞 TEEEEST**

* Tester que vous pouvez lire et écrire dans le dossier /srv/nfs depuis web1.server2.tp3*

`[paul@web1 nfs]$ sudo nano toto.txt`

```
[paul@web1 nfs]$ cat toto.txt
test 123456789 toto lol
```

### IV. Un peu de théorie : TCP et UDP


