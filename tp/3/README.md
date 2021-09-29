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
| `router.tp3` | `10.3.0.10/26`         | `10.3.1.10/25`         | `10.3.2.10/28`         | Carte NAT             |
| ...          | ...                  | ...                  | ...                  | `10.3.?.?/?`          |

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

* A l'aide de la commande traceroute, prouver que marcel.client1.tp3 passe par router.tp3 pour sortir de son réseau
