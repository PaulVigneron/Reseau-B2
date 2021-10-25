# TP4 : Vers un réseau d'entreprise

## I. Dumb switch

### 3. Setup topologie 1

**🌞 Commençons simple**

* Définissez les IPs statiques sur les deux VPCS

```
PC1 : 10.1.1.1 255.255.255.0

PC1> show ip all

NAME   IP/MASK              GATEWAY           MAC                DNS
PC1    10.1.1.1/24          0.0.0.0           00:50:79:66:68:00
```

```
PC2> show ip all

NAME   IP/MASK              GATEWAY           MAC                DNS
PC2    10.1.1.2/24          0.0.0.0           00:50:79:66:68:01
```

* Ping un VPCS depuis l'autre

```
PC1> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=13.721 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=11.087 ms
84 bytes from 10.1.1.2 icmp_seq=3 ttl=64 time=13.048 ms
```

```
PC2> ping 10.1.1.1

84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=19.488 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=17.425 ms
```

## II. VLAN

### 3. Setup topologie 2
 
**🌞 Adressage**

* Définissez les IPs statiques sur tous les VPCS

```
NAME   IP/MASK              GATEWAY           MAC                DNS
PC3    10.1.1.3/24          0.0.0.0           00:50:79:66:68:02
```

* Vérifiez avec des ping que tout le monde se ping

```
PC1> ping 10.1.1.3

84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=15.070 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=12.456 ms
```

```
PC2> ping 10.1.1.3

84 bytes from 10.1.1.3 icmp_seq=1 ttl=64 time=18.054 ms
84 bytes from 10.1.1.3 icmp_seq=2 ttl=64 time=14.211 ms
```

```
PC3> ping 10.1.1.1

84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=16.704 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=12.559 ms
```

**🌞 Configuration des VLANs**

* Déclaration des VLANs sur le switch sw1

```
Switch(config)#interface gigabitEthernet0/0
Switch(config-if)#switchport mode access
Switch(config-if)#switchport access vlan 10
```


```
Switch#show vlan br

VLAN Name                             Status    Ports
---- -------------------------------- --------- -------------------------------

[...]
10   1O                               active    Gi0/0, Gi0/1
20   20                               active    Gi0/2
```

* Pc1 et pc2 doivent toujours pouvoir se ping

```
PC1> ping 10.1.1.2

84 bytes from 10.1.1.2 icmp_seq=1 ttl=64 time=34.214 ms
84 bytes from 10.1.1.2 icmp_seq=2 ttl=64 time=7.661 ms
```

```
PC2> ping 10.1.1.1

84 bytes from 10.1.1.1 icmp_seq=1 ttl=64 time=18.721 ms
84 bytes from 10.1.1.1 icmp_seq=2 ttl=64 time=16.526 ms
```

* Pc3 ne ping plus personne

```
PC2> ping 10.1.1.3

host (10.1.1.3) not reachable
```

```
PC1> ping 10.1.1.3

host (10.1.1.3) not reachable
```

### III. Routing

* Ajout des ports du switches dans le bon VLAN

```
10   clients                          active    Gi0/1, Gi0/2
11   admins                           active    Gi0/3
12   servers                          active    Gi1/0
```


* Il faudra ajouter le port qui pointe vers le routeur comme un trunk : c'est un port entre deux équipements réseau (un switch et un routeur)

```
Switch#conf t
Switch(config)#interface gigabitEthernet 0/0
Switch(config-if)#switchport trunk encapsulation dot1q
Switch(config-if)#switchport mode trunk
Switch(config-if)#switchport trunk allowed vlan add 10,11,12
Switch(config-if)#exit
```

**➜ Pour le routeur**

**🌞 Config du routeur**

```
R1#conf t

R1(config)#interface FastEthernet 0/0.10
R1(config-subif)#encapsulation dot1Q 10
R1(config-subif)#ip addr 10.1.1.254 255.255.255.0
R1(config-subif)#exit

R1(config-subif)#interface FastEthernet 0/0.11
R1(config-subif)#encapsulation dot1Q 11
R1(config-subif)#ip addr 10.2.2.254 255.255.255.0
R1(config-subif)#exit

R1(config)#interface FastEthernet 0/0.12
R1(config-subif)#encapsulation dot1Q 12
R1(config-subif)#ip addr 10.3.3.254 255.255.255.0
R1(config-subif)#exit
```


```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES unset  up                    up
FastEthernet0/0.10         10.1.1.254      YES manual up                    up
FastEthernet0/0.11         10.2.2.254      YES manual up                    up
FastEthernet0/0.12         10.3.3.254      YES manual up                    up
FastEthernet0/0.20         unassigned      YES unset  up                    up
FastEthernet1/0            unassigned      YES unset  administratively down down
FastEthernet2/0            unassigned      YES unset  administratively down down
FastEthernet3/0            unassigned      YES unset  administratively down down
```

**🌞 Vérif**

```
PC1> ping 10.1.1.254

10.1.1.254 icmp_seq=1 timeout
84 bytes from 10.1.1.254 icmp_seq=2 ttl=255 time=24.069 ms
84 bytes from 10.1.1.254 icmp_seq=3 ttl=255 time=20.802 ms
```

```
PC2> ping 10.1.1.254

10.1.1.254 icmp_seq=1 timeout
84 bytes from 10.1.1.254 icmp_seq=2 ttl=255 time=21.835 ms
84 bytes from 10.1.1.254 icmp_seq=3 ttl=255 time=30.085 ms
```

```
adm1> ping 10.2.2.254

84 bytes from 10.2.2.254 icmp_seq=1 ttl=255 time=14.873 ms
84 bytes from 10.2.2.254 icmp_seq=2 ttl=255 time=23.836 ms
```

```
[paul@web1 ~]$ ping 10.3.3.254
PING 10.3.3.254 (10.3.3.254) 56(84) bytes of data.
64 bytes from 10.3.3.254: icmp_seq=1 ttl=255 time=64.1 ms
64 bytes from 10.3.3.254: icmp_seq=2 ttl=255 time=20.9 ms
```

## IV. NAT

### 3. Setup topologie 4

**🌞 Ajoutez le noeud Cloud à la topo**

* Côté routeur, il faudra récupérer un IP en DHCP

```
R1#show ip int br
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            unassigned      YES unset  up                    up
FastEthernet0/0.10         10.1.1.254      YES manual up                    up
FastEthernet0/0.11         10.2.2.254      YES manual up                    up
FastEthernet0/0.12         10.3.3.254      YES manual up                    up
FastEthernet0/0.20         unassigned      YES unset  up                    up
FastEthernet1/0            unassigned      YES DHCP   up                    up
FastEthernet2/0            unassigned      YES unset  administratively down down
FastEthernet3/0            unassigned      YES unset  administratively down down
```

* Vous devriez pouvoir ping 1.1.1.1

```
R1#ping 1.1.1.1

Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 1.1.1.1, timeout is 2 seconds:
.!!!!
Success rate is 80 percent (4/5), round-trip min/avg/max = 24/28/32 ms
```

**🌞 Configurez le NAT**


```
R1#conf t

R1(config)#interface fastEthernet 1/0
R1(config-if)#ip nat outside
R1(config-if)#exit

R1(config)#interface fastEthernet 0/0
R1(config-if)#ip nat inside
R1(config-if)#exit

R1(config)#access-list 1 permit any
R1(config)#ip nat inside source list 1 interface fastEthernet 1/0 overload
```

**🌞 Test**

* Ajoutez une route par défaut (si c'est pas déjà fait)

1. sur les VPCS

```
PC1> ip 10.1.1.1/24 10.1.1.254
Checking for duplicate address...
PC1 : 10.1.1.1 255.255.255.0 gateway 10.1.1.254
```

```
PC2> ip 10.1.1.2/24 10.1.1.254
Checking for duplicate address...
PC2 : 10.1.1.2 255.255.255.0 gateway 10.1.1.254
```

```
adm1> ip 10.2.2.1/24 10.2.2.254
Checking for duplicate address...
adm1 : 10.2.2.1 255.255.255.0 gateway 10.2.2.254
```

3. sur la machine Linux

`[paul@web1 ~]$ sudo ip route add default via 10.3.3.254 dev enp0s3`

```
# Created by anaconda
GATEWAY=10.3.3.254
```

* Configurez l'utilisation d'un DNS
 
* Sur les VPCS

```
PC1> ip dns 1.1.1.1

PC2> ip dns 1.1.1.1

adm1> ip dns 1.1.1.1
```


* Sur la machine Linux
 
```
[paul@web1 ~]$ sudo cat /etc/resolv.conf 
# Generated by NetworkManager
search auvence.co servers.tp4
nameserver 1.1.1.1
```
 
* Vérifiez un ping vers un nom de domaine

```
PC1> ping google.com
google.com resolved to 142.250.179.110

84 bytes from 142.250.179.110 icmp_seq=1 ttl=113 time=57.434 ms
84 bytes from 142.250.179.110 icmp_seq=2 ttl=113 time=55.432 ms
```

```
PC2> ping google.com
google.com resolved to 142.250.179.110

84 bytes from 142.250.179.110 icmp_seq=1 ttl=113 time=40.075 ms
84 bytes from 142.250.179.110 icmp_seq=2 ttl=113 time=171.355 ms
```

```
adm1> ping google.com
google.com resolved to 142.250.201.174

84 bytes from 142.250.201.174 icmp_seq=1 ttl=113 time=46.332 ms
84 bytes from 142.250.201.174 icmp_seq=2 ttl=113 time=64.035 ms
```

```
[paul@web1 ~]$ ping google.com
PING google.com (142.250.201.174) 56(84) bytes of data.
64 bytes from par21s23-in-f14.1e100.net (142.250.201.174): icmp_seq=1 ttl=113 time=37.4 ms
64 bytes from par21s23-in-f14.1e100.net (142.250.201.174): icmp_seq=2 ttl=113 time=43.3 ms
--- google.com ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 37.396/40.345/43.295/2.956 ms
```

🌞  **Vous devez me rendre le `show running-config` de tous les équipements**



🌞  **Mettre en place un serveur DHCP dans le nouveau bâtiment**

```
default-lease-time 900;
max-lease-time 10800;
ddns-update-style none;
authoritative;
subnet 10.1.1.0 netmask 255.255.255.0 {
  range 10.1.1.3 10.1.1.253;
  option routers 10.1.1.254;
  option subnet-mask 255.255.255.0;
  option domain-name-servers 10.1.1.254;

}
```
