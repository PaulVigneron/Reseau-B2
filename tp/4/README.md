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
