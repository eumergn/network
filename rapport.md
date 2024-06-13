# TP a Rendre Reseaux
[![GIT](https://img.icons8.com/?size=100&id=44900&format=png&color=000000)](https://git.unistra.fr/armagan)

> **ARMAGAN Omer** / 21918288


#### Question 1.
Pour 250 machines, on a besoin de (2^8)256 adresses IP (250 machines + 2 adresses(broadcast&00)), c-a-d, 32-8=24,
Donc, un préfixe de /24 (256 adresses) est approprié.


#### Question 2.

| *Sous-réseau A*  |   192.168.96.0/24 |
| ------ | ------ |
| Adresse de réseau |     192.168.96.0 |
| Masque de sous-réseau | 255.255.255.0 |
| Première adresse IP |   192.168.96.1 |
| Dernière adresse IP |   192.168.96.254 |
| Adresse de broadcast |  192.168.96.255 |

| *Sous-réseau B*  |   192.168.97.0/24 |
| ------ | ------ |
| Adresse de réseau |     192.168.97.0 |
| Masque de sous-réseau | 255.255.255.0 |
| Première adresse IP |   192.168.97.1 |
| Dernière adresse IP |   192.168.97.254 |
| Adresse de broadcast |  192.168.97.255 |


#### Question 3&4.
J'ai choisi les adresses suivantes: 
> PC1 -> 192.168.96.1/24
  PC2 -> 192.168.97.1/24
    
PC1 dans sous-reseau A, on ecrit dans le terminal de PC1>>
```sh
ip link set eth0 up
ip addr add 192.168.96.1/24 dev eth0
```
PC2 dans sous-reseau B, on ecrit dans le terminal de PC2 >>
```sh
ip link set eth0 up
ip addr add 192.168.97.1/24 dev eth0
```
Apres on peut verifier en ecrivant `ip a`, on pourrait tres bien voir;

1: lo: <LOOPBACK,UP,LOWER_UP> mtu 16436 qdisc noqueue state UNKNOWN 
link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
inet 127.0.0.1/8 scope host lo
inet6 ::1/128 scope host 
valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state `UP` qlen 1000
link/ether 0c:1d:4b:53:8d:00 brd ff:ff:ff:ff:ff:ff
inet `192.168.96.1/24 scope global eth0`
inet6 fe80::e1d:4bff:fe53:8d00/64 scope link 
valid_lft forever preferred_lft forever


#### Question 5.
Pour configurer les interfaces du router1, on ouvre le terminal de PC2(car c'est la machine qui est connecte directement au router1) et on ecrit la commande pour pouvoir passer au terminal de switch >>

    screen /dev/ttyS0
et la on est dans le switch, pour transformer switch en router, on ecrit les commandes suivantes >>
    
    en
    conf t
    ip routing      // permet de router les paquets entre differents reseaux IP

et la on configure l'interface de router1 de cote PC2 >>
```sh
interface gigabitEthernet 1/0/1
ip address 192.168.97.254 255.255.255.0
no shutdown               // pour activer l'interface du routeur1
```
et maintenant on fait le changement d'interface >>

	exit                // pas besoin de faire _end_ , avec "exit" on sera toujours dans la _conf t_
	
et donc la on fait pareil pour l'interface de router1 de cote PC1 >>
```sh
interface fastEthernet 1/0/2
no switchport       // pour convertir un port du mode couche 2 (switch) au couche 3 (routage)
ip address 192.168.96.254 255.255.255.0
no shutdown
```
et donc la, pour verifier la configuration on peut taper les commmandes suivantes >>

    end                     // pour pouvoir sortir de "conf t"
    show ip interface brief

```sh
Switch# show ip interface brief
Interface              IP-Address      OK? Method Status                Protocol
Vlan1                  unassigned      YES unset  administratively down down
GigabitEthernet1/0/1   192.168.97.254  YES manual up                    up
GigabitEthernet1/0/2   192.168.96.254  YES manual up                    up
GigabitEthernet1/0/3   unassigned      YES manual down                  down
GigabitEthernet1/0/4   unassigned      YES manual down                  down
...
...
```

#### Question 6.
Pour definir une route vers le sous-reseau B de PC1, on ecrit les commandes suivantes dans le terminal de PC1 >>
```sh
ip route add 192.168.97.0/24 via 192.168.96.254
```
#### Question 7.
Pour definir une route vers le sous-reseau A de PC2, on ecrit les commandes suivantes dans le terminal de PC2 >>
_(on peut soit ouvrir un autre terminal, soit dans le terminal de router1 qu'on avait ouvert en tapant "CTRL A+D", on peut retourner au terminal de PC2)_
```sh
ip route add 192.168.96.0/24 via 192.168.97.254
```
#### Question 8.
(Voir la capture d'ecran)

#### Question 9.
(Voir la capture d'ecran)

#### Question 10.
(Voir la capture d'ecran)
	
#### Question 11&12.
(Voir la capture d'ecran)
Dans la capture j'ai;
- Who has 192.168.96.254? :
C'est une requête ARP où l'hôte avec l'adresse IP 192.168.96.1 demande l'adresse MAC de l'hôte ayant l'adresse IP 192.168.96.254.
- Tell 192.168.96.1 :
Cette requête indique que la réponse à la question "Qui a l'adresse IP 192.168.96.254 ?" doit être envoyée à l'adresse IP 192.168.96.1.
- 192.168.96.254 is at 84:5a:3e:13:6e:41 :
C'est une réponse ARP indiquant que l'adresse IP 192.168.96.254 est associée à l'adresse MAC 84:5a:3e:13:6e:41.

Donc,la requête et la réponse ARP capturées dans Wireshark montrent le processus de résolution d'adresse IP en adresse MAC. L'appareil avec l'adresse IP 192.168.96.1 cherche à communiquer avec l'appareil ayant l'adresse IP 192.168.96.254, et la réponse fournit l'adresse MAC nécessaire pour cette communication directe sur le réseau local.

#### Question 14.
Comme on a transforme le switch en router2, on aura 3 sous-reseaux differents 
- Sous-reseau A       ->    `192.168.96.0`
- Sous-reseau B       ->    `192.168.97.0`
- Sous-reseau R2_R1   ->    `192.168.98.0`

Donc, quand on teste la connectivite avec la commande `ping 192.168.97.1` dans le terminal de PC1, le sous-reseau R1_R2 nest pas reconnu !
Donc, on aura cela dans le output:  
```sh
PING 192.168.97.1 (192.168.97.1) 56(84) bytes of data.
From 192.168.96.1 icmp_seq=1 Destination Host Unreachable
From 192.168.96.1 icmp_seq=2 Destination Host Unreachable
From 192.168.96.1 icmp_seq=3 Destination Host Unreachable
From 192.168.96.1 icmp_seq=4 Destination Host Unreachable
From 192.168.96.1 icmp_seq=5 Destination Host Unreachable
--- 192.168.97.1 ping statistics ---
5 packets transmitted, 0 received, +5 errors, 100% packet loss, time 5009ms
```
(Voir la capture d'ecran)

il faudrait egalement prevoir et configurer dans le terminal de router, car l'addresse du port du router1 de cote PC1 qu'on choisi nest plus dans le meme sous-reseau, donc, il faudrait mettre cet addresse du port du router2 de cote PC1
(Voir l'explication que j'ai fait en papier , je l'ai ajouté en format pdf)

#### Question 15.
Donc pour pouvoir faire la connectivité entre PC1 et PC2, il faudrait configurer les interfaces de routers car on a un sous-reseau different qu'on avait pas encore configuré.
##### Router2
D'abord pour pouvoir acceder au router2 depuis le terminal de PC1, on tape les commandes suivantes:
    
    screen /dev/ttyS0
    en
    conf t
    ip routing
et une fois on est dans le router2, j'ai configure dabord l'interface de cote PC1:
```sh
interface GigabitEthernet 1/0/1
ip address 192.168.96.254 255.255.255.0
no shutdown
```
et maintenant on fait le changement d'interface >>

	exit
et la, on configure l'interface de cote R2_R1 :
```sh
ip address 192.168.97.0 255.255.255.0 192.168.98.254
interface GigabitEthernet 1/0/2
ip address 192.168.98.253 255.255.255.0
no switchport
no shutdown
```
##### Router1
et maintenant on passe au router1 depuis le terminal de PC2, on tape les commandes suivantes:

    screen /dev/ttyS0
    en
et une fois on est dans le router1, on configure l'interface de cote R2_R1(car avant on avait l'addresse `192.168.96.254` ce qui etait dans le sous-reseau A):
```sh
conf t
ip address 192.168.96.0 255.255.255.0 192.168.98.253
interface GigabitEthernet 1/0/2
no ip address 192.168.96.254 255.255.255.0
ip address 192.168.98.254 255.255.255.0
no switchport
no shutdown
end
```
et finalement pour la derniere verification de la configuration , on peut ecrire dans les deux terminals de routers(soyez sur que on a bien ecrit `end` avant d'ecrire la commande):
    
    show ip interface brief         // permet de voir les addresses des interfaces
et

    show ip route                   // permet de voir les routes 
    
et on voit que tout a l'air `bon :)`

#### Question 16&17.

`traceroute 192.168.97.1`
```sh
traceroute to 192.168.97.1 (192.168.97.1), 30 hops max, 60 byte packets
 1  r-dptinfo-101.u-strasbg.fr (130.79.7.254)  0.396 ms  0.268 ms  0.185 ms
 2  r-imfs-28.u-strasbg.fr (130.79.208.190)  0.586 ms  0.513 ms  0.224 ms
 3  dc-e017-a12-bl2-100-120-0-16.di.unistra.fr (100.120.0.16)  0.705 ms dc-e017-a11-bl2-100-120-0-22.di.unistra.fr (100.120.0.22)  0.626 ms  0.541 ms
 4  192.168.96.254 (192.168.96.254)  0.402 ms  0.263 ms  0.184 ms
 5  * * espla-rc1-130-79-20-254.u-strasbg.fr (130.79.20.254)  0.438 ms
 6  192.168.98.253 (192.168.98.253)  0.482 ms  0.502 ms  0.483 ms
 7  192.168.98.254 (192.168.98.254)  0.625 ms  0.626 ms  0.559 ms
 8  * * *
 9  192.168.97.254 (192.168.97.254)  0.518 ms  0.476 ms  0.213 ms
10  192.168.97.1 (192.168.97.1)  0.218 ms  0.176 ms  0.218 ms
11  * * *
12  * * *
...
```
**A quoi sert le traceroute?**
C'est pour diagnostiquer les problèmes de connectivité reseau en identifiant les sauts (routers) que les paquets traversent pour atteindre a la destination. Ca nous aide à localiser les points de défaillance ou les ralentissements dans le chemin du reseau...

> `TTL = Time-To-Live`

Traceroute fonctionne en envoyant des messages à la destination avec un delai de vie (TTL) qui diminue à chaque saut. Chaque saut est un routeur sur le chemin vers la destination. Lorsqu'un routeur reçoit un message avec un TTL à zero, il le rejette et envoie un message de retour. Traceroute enregistre cet emplacement. Ensuite, il envoie le meme message avec un TTL plus élevé pour atteindre le saut suivant. Il répète ce processus jusqu'à ce qu'il atteigne la destination. En regardant les réponses, traceroute peut dire combien de sauts il y a entre vous et la destination, et les adresses IP de ces sauts.