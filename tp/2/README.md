## TP2 : On va router des trucs

### I. ARP

🌞Générer des requêtes ARP

* ping 10.2.1.12

```
[paul@node1 ~]$ [paul@node1 ~]$ ping 10.2.1.12
PING 10.2.1.12 (10.2.1.12) 56(84) bytes of data.
64 bytes from 10.2.1.12: icmp_seq=1 ttl=64 time=0.712 ms
64 bytes from 10.2.1.12: icmp_seq=2 ttl=64 time=1.22 ms
^C
--- 10.2.1.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1050ms
rtt min/avg/max/mdev = 0.712/0.966/1.221/0.256 ms
```

* ping 10.2.1.11

```
[paul@node2 ~]$ [paul@node2 ~]$ ping 10.2.1.11
PING 10.2.1.11 (10.2.1.11) 56(84) bytes of data.
64 bytes from 10.2.1.11: icmp_seq=1 ttl=64 time=0.405 ms
64 bytes from 10.2.1.11: icmp_seq=2 ttl=64 time=1.05 ms
^C
--- 10.2.1.11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1053ms
rtt min/avg/max/mdev = 0.405/0.725/1.045/0.320 ms
```

* ip n s

```
10.2.1.1 dev enp0s8 lladdr 0a:00:27:00:00:0a REACHABLE
10.2.1.12 dev enp0s8 lladdr 08:00:27:39:2e:04 STALE
```

```
10.2.1.1 dev enp0s8 lladdr 0a:00:27:00:00:0a DELAY
10.2.1.11 dev enp0s8 lladdr 08:00:27:b8:62:ef STALE
```

```
[paul@node2 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:39:2e:04 brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.12/24 brd 10.2.1.255 scope global noprefixroute enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe39:2e04/64 scope link
       valid_lft forever preferred_lft forever
```
🌞Analyse de trames

* sudo ip neigh flush all`

```
[paul@node1 ~]$ ip n s
10.2.1.1 dev enp0s8 lladdr 0a:00:27:00:00:0a 
```

```
[paul@node2 ~]$ ip n s
10.2.1.1 dev enp0s8 lladdr 0a:00:27:00:00:0a REACHABLE
```

* sudo tcpdump -i enp0s8 -w tp2_arp.pcap not port 22`

```
[paul@node2 ~]$ ping 10.2.1.11
PING 10.2.1.11 (10.2.1.11) 56(84) bytes of data.
64 bytes from 10.2.1.11: icmp_seq=1 ttl=64 time=0.559 ms
64 bytes from 10.2.1.11: icmp_seq=2 ttl=64 time=1.34 ms
64 bytes from 10.2.1.11: icmp_seq=3 ttl=64 time=1.15 ms
^C
--- 10.2.1.11 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2029ms
rtt min/avg/max/mdev = 0.559/1.017/1.344/0.334 ms
[paul@node2 ~]$
```

```
[paul@node1 ~]$ sudo tcpdump -i enp0s8 -w tp2_arp.pcap not port 22
dropped privs to tcpdump
tcpdump: listening on enp0s8, link-type EN10MB (Ethernet), capture size 262144 bytes
^C10 packets captured
11 packets received by filter
0 packets dropped by kernel
[paul@node1 ~]$ ls
tp2_arp.pcap
```

![](https://i.imgur.com/LiwnPWG.png)

| ordre | type trame  | source                   | destination                |
|-------|-------------|--------------------------|----------------------------|
| 1     | Requête ARP | `node2` `08:00:27:39:2e:04` | Broadcast `FF:FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | `node1` `08:00:27:b8:62:ef` | `node2` `08:00:27:39:2e:04`   |
| 3   | Requête Ping  |  `node2` `10.2.1.12`                     | `node1` `10.2.1.11`
| 4     |Requête Ping | `node1` `10.2.1.11` | `node2` `10.2.1.12`   |               |

## II. Routage

🌞Activer le routage sur le noeud router.net2.tp2

```
[paul@router ~]$ sudo firewall-cmd --add-masquerade --zone=public --permanent
success
```

```
[paul@router ~]$ sudo firewall-cmd --add-masquerade --zone=public
success
```

🌞Ajouter les routes statiques nécessaires pour que node1.net1.tp2 et marcel.net2.tp2 puissent se ping

* sudo nano /etc/sysconfig/network-scripts/route-enp0s8`

```
10.2.2.0/24 via 10.2.1.254
10.2.1.0/24 via 10.2.2.254
```

```
[paul@node1 ~]$ ping 10.2.2.12
PING 10.2.2.12 (10.2.2.12) 56(84) bytes of data.
64 bytes from 10.2.2.12: icmp_seq=1 ttl=63 time=1.42 ms
64 bytes from 10.2.2.12: icmp_seq=2 ttl=63 time=2.18 ms
^C
--- 10.2.2.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 1.416/1.795/2.175/0.381 ms
```

```
[paul@marcel ~]$ ping 10.2.1.11
PING 10.2.1.11 (10.2.1.11) 56(84) bytes of data.
64 bytes from 10.2.1.11: icmp_seq=1 ttl=63 time=0.537 ms
64 bytes from 10.2.1.11: icmp_seq=2 ttl=63 time=0.657 ms
^C
--- 10.2.1.11 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1040ms
rtt min/avg/max/mdev = 0.537/0.597/0.657/0.060 ms
```

🌞Analyse des échanges ARP

* sudo ip neigh flush all`

```
[paul@node1 ~]$ [paul@node1 ~]$ ping 10.2.2.12
PING 10.2.2.12 (10.2.2.12) 56(84) bytes of data.
64 bytes from 10.2.2.12: icmp_seq=1 ttl=63 time=1.03 ms
64 bytes from 10.2.2.12: icmp_seq=2 ttl=63 time=0.670 ms
^C
--- 10.2.2.12 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.670/0.851/1.033/0.183 ms
```

*essayez de déduire un peu les échanges ARP qui ont eu lieu :*

```
[paul@node1 ~]$ ip n s
10.2.1.1 dev enp0s8 lladdr 0a:00:27:00:00:0a DELAY
10.2.1.254 dev enp0s8 lladdr 08:00:27:9f:6e:62 STALE
```
On peux voir que la demande à bien était envoyer avec la passerelle qui s'affiche dans node1.

```
[paul@router ~]$ [paul@router ~]$ ip n s
10.2.2.12 dev enp0s9 lladdr 08:00:27:64:d5:c4 STALE
10.33.10.155 dev enp0s8  FAILED
10.2.1.1 dev enp0s8 lladdr 0a:00:27:00:00:0a DELAY
10.2.1.11 dev enp0s8 lladdr 08:00:27:b8:62:ef STALE
10.33.10.148 dev enp0s8  FAILED
10.33.10.2 dev enp0s8  FAILED
```
On voit que router à bien réceptionner la demande et il l'envoie à Marcel.

```
[paul@marcel ~]$ ip n s
10.2.2.254 dev enp0s8 lladdr 08:00:27:a7:6f:2e STALE
10.2.2.1 dev enp0s8 lladdr 0a:00:27:00:00:52 DELAY
```
On remarque que Marcel reçoit donc bien le ping de node1.

| ordre | type trame  | IP source | MAC source                | IP destination | MAC destination            |
|-------|-------------|-----------|---------------------------|----------------|----------------------------|
| 1     | Requête ARP | x         | `node1` `AA:BB:CC:DD:EE`  | x              | Broadcast `FF:FF:FF:FF:FF` |
| 2     | Réponse ARP | 10.2.2.1         | `marcel` `AA:AA:AA:AA:AA` | x              | `node1` `AA:BB:CC:DD:EE`   |
| ...   | ...         | ...       | ...                       |                |                            |
| ?     | Ping        | ?         | ?                         | ?              | ?                          |
| ?     | Pong        | ?         | ?                         | ?              | ?                          |

🌞Donnez un accès internet à vos machines

```
node1 : default via 10.2.1.11
marcel : default via 10.2.2.11
```
```
[paul@node1 ~]$ [paul@node1 ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=113 time=22.8 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=113 time=22.6 ms
```

```
[paul@marcel ~]$ [paul@marcel ~]$ ping 8.8.8.8
PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
64 bytes from 8.8.8.8: icmp_seq=1 ttl=113 time=23.2 ms
64 bytes from 8.8.8.8: icmp_seq=2 ttl=113 time=22.2 ms
```

* dig gitlab.com

```
[paul@node1 ~]$ [paul@node1 ~]$ dig gitlab.com

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> gitlab.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 840
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;gitlab.com.                    IN      A

;; ANSWER SECTION:
gitlab.com.             149     IN      A       172.65.251.78

;; Query time: 21 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Thu Sep 23 16:40:09 CEST 2021
;; MSG SIZE  rcvd: 55
```

* dig diglab.com

```
[paul@marcel ~]$ [paul@marcel ~]$ dig diglab.com

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> diglab.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 54210
;; flags: qr rd ra; QUERY: 1, ANSWER: 4, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;diglab.com.                    IN      A

;; ANSWER SECTION:
diglab.com.             10800   IN      CNAME   comingsoon.namebright.com.
comingsoon.namebright.com. 300  IN      CNAME   cdl-lb-1356093980.us-east-1.elb.amazonaws.com.
cdl-lb-1356093980.us-east-1.elb.amazonaws.com. 60 IN A 54.204.55.163
cdl-lb-1356093980.us-east-1.elb.amazonaws.com. 60 IN A 54.85.93.188

;; Query time: 410 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Thu Sep 23 16:38:28 CEST 2021
;; MSG SIZE  rcvd: 163
```

* ping ynov.com

```
ping ynov.com
PING ynov.com (92.243.16.143) 56(84) bytes of data.
64 bytes from xvm-16-143.dc0.ghst.net (92.243.16.143): icmp_seq=1 ttl=52 time=18.1 ms
64 bytes from xvm-16-143.dc0.ghst.net (92.243.16.143): icmp_seq=2 ttl=52 time=32.1 ms
^C
--- ynov.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 18.107/25.116/32.126/7.011 ms
```

🌞Analyse de trames

| ordre | type trame | IP source           | MAC source               | IP destination | MAC destination |     |
|-------|------------|---------------------|--------------------------|----------------|-----------------|-----|
| 1     | ping       | `node1` `10.2.1.12` | `node1` `PcsCompu_a5:51b2` | `8.8.8.8`      | PcsCompu_ea:b3d6               |     |
| 2     | pong       | ``8.8.8.8``                 |`` PcsCompu_ea``:b3d                      | node1 10.2.1.12            | node1 PcsCompu_a5:51b2             | ... |

### III. DHCP

🌞Sur la machine node1.net1.tp2, vous installerez et configurerez un serveur DHCP (go Google "rocky linux dhcp server").

```
default-lease-time 900;
max-lease-time 10800;
ddns-update-style none;
authoritative;
subnet 10.2.1.0 netmask 255.255.255.0 {
  range 10.2.1.2 10.2.1.10;
  option routers 10.2.1.254;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 1.1.1.1;

}
```

```
[paul@node2 ~]$ [paul@node2 ~]$ ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:cc:55:bf brd ff:ff:ff:ff:ff:ff
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:80:22:38 brd ff:ff:ff:ff:ff:ff
    inet 10.2.1.1/24 brd 10.2.1.255 scope global dynamic noprefixroute enp0s8
       valid_lft 826sec preferred_lft 826sec
    inet6 fe80::a00:27ff:fe80:2238/64 scope link
       valid_lft forever preferred_lft forever
```

🌞Améliorer la configuration du DHCP

* ip r s

```
[paul@node2 ~]$ [paul@node2 ~]$ ip r s
default via 10.2.1.11 dev enp0s8 proto dhcp metric 102
10.2.1.0/24 dev enp0s8 proto kernel scope link src 10.2.1.1 metric 102
```

* ping 10.2.1.11

```
[paul@node2 ~]$ [paul@node2 ~]$ ping 10.2.1.11
PING 10.2.1.11 (10.2.1.11) 56(84) bytes of data.
64 bytes from 10.2.1.11: icmp_seq=1 ttl=64 time=0.828 ms
64 bytes from 10.2.1.11: icmp_seq=2 ttl=64 time=0.518 ms
```

* ip r s

```
[paul@node2 ~]$ [paul@node2 ~]$ ip r s
default via 10.2.1.11 dev enp0s8 proto dhcp metric 102
10.2.1.0/24 dev enp0s8 proto kernel scope link src 10.2.1.1 metric 102
```

* ping 10.2.2.12

```
[paul@node2 ~]$ [paul@node2 ~]$ ping 10.2.2.12
PING 10.2.2.12 (10.2.2.12) 56(84) bytes of data.
64 bytes from 10.2.2.12: icmp_seq=1 ttl=63 time=0.895 ms
64 bytes from 10.2.2.12: icmp_seq=2 ttl=63 time=0.981 ms
```

* ping 1.1.1.1

```
[paul@node2 ~]$ [paul@node2 ~]$  ping 1.1.1.1
PING 1.1.1.1 (1.1.1.1) 56(84) bytes of data.
64 bytes from 1.1.1.1: icmp_seq=1 ttl=62 time=4.21 ms
64 bytes from 1.1.1.1: icmp_seq=2 ttl=62 time=3.84 ms
```

* dig ynov.com

```
[paul@node2 ~]$ [paul@node2 ~]$ dig ynov.com

; <<>> DiG 9.11.26-RedHat-9.11.26-4.el8_4 <<>> ynov.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 16281
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
;; QUESTION SECTION:
;ynov.com.                      IN      A

;; ANSWER SECTION:
ynov.com.               2739    IN      A       92.243.16.143

;; Query time: 63 msec
;; SERVER: 1.1.1.1#53(1.1.1.1)
;; WHEN: Thu Sep 23 19:51:12 CEST 2021
;; MSG SIZE  rcvd: 53
```

### 2. Analyse de trames

**🌞Analyse de trames**

***Vider les tables ARP*** 

`sudo ip neigh flush all`

***Lancer une capture pour avoir un échange DHCP***

`sudo tcpdump -i enp0s8 -w tp2_dhcp.pcap not port 22`

***Demander une nouvelle ip pour générer un échange DHCP***

`sudo dhclient`

```
1   pcsCompu_a5:51:b2  ||   0a:00:27:00:00:3f  || ARP||42|| Who has 10.2.1.1?Tell 10.2.1.12
2   0a:00:27:00:00:3f || pcsCompu_a5:51:b2 || ARP || 60 || 10.2.1.1 is at 0a:00:27:00:00:3f
3   10.2.1.3 || 255.255.255.255 || DCHP || 342 DCHP - transaction ID 0xc2d1f91c
4   10.2.1.3 || 255.255.255.255 || DHCP || 342 DCHP - transaction ID 0xc2d1f91c
```
