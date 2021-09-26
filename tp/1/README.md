# B2_TP1_Réseau

## I. Exploration locale en solo

 #### 🌞 Affichez les infos des cartes réseau de votre PC : 
 
  ***ipconfig -all***
  
    Carte réseau sans fil Wi-Fi :
    Adresse IPv4. . . . . . . . . . . . . .: 10.33.0.20
    Adresse physique . . . . . . . . . . . : 74-29-AF-33-1B-69
    
   ```
Carte Ethernet Ethernet :
   Adresse physique . . . . . . . . . . . : 44-8A-5B-F1-FC-2C
   Adresse IPv4 : Média déconnecté
```
#### 🌞 Affichez votre gateway : 

***ipconfig***
    
 `Passerelle par défaut. . . . . . . . . : 10.33.3.253`
 
 #### 🌞 Trouvez comment afficher les informations sur une carte IP (change selon l'OS)
 
 ![](image/modifier_ip.png)

#### 🌞 à quoi sert la gateway dans le réseau d'YNOV ?

> *Le gateway dans le réseau d'YNOV permet de relier les réseaux entre eux.*

## 2. Modifications des informations

#### 🌞 Utilisez l'interface graphique de votre OS pour changer d'adresse IP :

![](/image/ip_physique.png)

#### 🌞 Il est possible que vous perdiez l'accès internet
>     Il y a possible de perde l'accès à internet car nous prenons une potentiel ip qui appartient déjà à une personne donc cela crée un conflit donc une erreur.

#### 🌞 Exploration de la table ARP

![](/image/arp.png)

> L'adresse MAC est situé sous l'Adresse physique on pourra donc voir les adresses MAC des différents réseaux.

#### 🌞 Et si on remplissait un peu la table ?

![](/image/arp_ping.png)

 Voici ci dessus les adresses IP avec les adresses MAC.
 
 ## C. nmap

####  🌞Utilisez nmap pour scanner le réseau de votre carte WiFi et trouver une adresse IP libre :

 ![](image/scan.PNG)

  ```
10.33.0.4             14-7d-da-10-65-78     dynamique
  10.33.0.21            b0-fc-36-ce-9c-89     dynamique
  10.33.0.175           74-d8-3e-f2-cc-95     dynamique
  10.33.0.197           14-7d-da-10-65-78     dynamique
  10.33.0.208           70-66-55-c5-4e-29     dynamique
  10.33.0.243           dc-21-5c-3b-a3-f3     dynamique
  10.33.1.69            a0-a4-c5-41-bf-4f     dynamique
  10.33.1.93            b8-9a-2a-3d-c1-1a     dynamique
  ...
```

## Modification d'adresse IP (part 2)

#### 🌞 Modifiez de nouveau votre adresse IP vers une adresse IP que vous savez libre grâce à nmap

`nmap -sn 10.33.0.20`

![](image/ping_scan.PNG)

![](image/configip.PNG)

![](image/ping_google.PNG)

#### 🌞 pour tester la connectivité à internet on fait souvent des requêtes simples vers un serveur internet connu

![](ping_8.8.8.8.PNG)

#### 🌞 utiliser un traceroute ou tracert pour bien voir que les requêtes passent par la passerelle choisie (l'autre le PC)

```
tracert 192.168.0.6

Détermination de l’itinéraire vers DESKTOP-DM8M0FQ [192.168.0.6]
avec un maximum de 30 sauts :

  1    <1 ms    <1 ms    <1 ms  DESKTOP-DM8M0FQ [192.168.0.6]

Itinéraire déterminé.
```

## 5. Petit chat privé

#### 🌞 sur le PC serveur  :

`nc.exe -l -p 8888`

#### 🌞 sur le PC client :

`nc.exe IP 8888`

## 6. Firewall

#### 🌞 Autoriser les ping

![](image/icmpv4.PNG)

![](image/icmpv6.PNG)

#### 🌞 Autoriser le traffic sur le port qu'utilise nc

```
PS C:\Paul\Deskop\netcat-win32-1.11\netcat-1.11> .\nc.exe -l -p 9718
salut
paul
```

## III. Manipulations d'autres outils/protocoles côté client

#### 🌞Exploration du DHCP, depuis votre PC

```
`Serveur DHCP . . . . . . . . . . . . . : 10.33.3.254`

   Bail obtenu. . . . . . . . . . . . . . : jeudi 16 septembre 2021 17:18:57
   Bail expirant. . . . . . . . . . . . . : jeudi 16 septembre 2021 19:18:57
```

#### 🌞 trouver l'adresse IP du serveur DNS que connaît votre ordinateur

`Serveurs DNS. . .  . . . . . . . . . . : 1.1.1.1`

#### 🌞 utiliser, en ligne de commande l'outil nslookup (Windows, MacOS) ou dig (GNU/Linux, MacOS) pour faire des requêtes DNS à la main

faites un lookup (lookup = "dis moi à quelle IP se trouve tel nom de domaine") :
```
C:\WINDOWS\system32>nslookup google.com
Serveur :   one.one.one.one
Address:  1.1.1.1

Réponse ne faisant pas autorité :
Nom :    google.com.auvence.co
Address:  178.32.154.7

C:\WINDOWS\system32>nslookup ynov.com
Serveur :   one.one.one.one
Address:  1.1.1.1

Réponse ne faisant pas autorité :
Nom :    ynov.com.auvence.co
Address:  178.32.154.7
```

faites un reverse lookup (= "dis moi si tu connais un nom de domaine pour telle IP") :

```
C:\WINDOWS\system32>nslookup 78.74.21.21
Serveur :   one.one.one.one
Address:  1.1.1.1

Nom :    host-78-74-21-21.homerun.telia.com
Address:  78.74.21.21


C:\WINDOWS\system32>nslookup 92.146.54.88
Serveur :   one.one.one.one
Address:  1.1.1.1

Nom :    apoitiers-654-1-167-88.w92-146.abo.wanadoo.fr
Address:  92.146.54.88
```

## IV. Wireshark

#### 🌞 utilisez le pour observer les trames qui circulent entre vos deux carte Ethernet. 

Un ping entre vous et la passerelle :

![](image/ping_pong.PNG)

Un netcat entre vous et votre mate, branché en RJ45 :

![](image/netcat.PNG)
