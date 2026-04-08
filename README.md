# Commandes d’architecture réseau avancée (VyOS : OSPF, BGP, OpenConnect VPN)

## Topologie
<img width="1057" height="774" alt="image" src="https://github.com/user-attachments/assets/1f71c468-592e-43e7-a18b-db45828ffcf0" />

## Tableau d’adressage

### VyOS Lille

| Réseau           | Utilisation   | VMnet   | Port |
|------------------|---------------|---------|------|
| 192.168.58.132   | NAT           | NAT     | eth0 |
| 192.168.12.0/30  | ISP ↔ Lille   | VMnet12 | eth1 |
| 172.16.0.0/30    | Lille ↔ ISP   | VMnet11 | eth2 |
| 10.0.2.1/24      | LAN Lille     | VMnet11 | eth3 |

### VyOS Boston

| Réseau           | Utilisation    | VMnet   | Port |
|------------------|----------------|---------|------|
| 192.168.58.128   | NAT            | NAT     | eth0 |
| 192.168.23.0/30  | Boston ↔ ISP   | VMnet12 | eth1 |
| 172.16.0.0/30    | Boston ↔ ISP   | VMnet11 | eth2 |
| 10.0.1.1/24      | LAN Boston     | VMnet10 | eth4 |

### VyOS ISP

| Réseau            | Utilisation   | VMnet   | Port |
|-------------------|---------------|---------|------|
| 192.168.58.131/24 | NAT           | NAT     | eth1 |
| 192.168.23.0/30   | Boston ↔ ISP  | VMnet10 | eth2 |
| 192.168.12.0/30   | ISP ↔ Lille   | VMnet12 | eth3 |

### Machine Boston

| Réseau            | Utilisation | VMnet   | Port  |
|-------------------|-------------|---------|-------|
| 10.0.1.0/24       | LAN Boston  | VMnet10 | ens33 |
| 192.168.58.100/24 | NAT         | NAT     | ens37 |

### Machine Lille

| Réseau            | Utilisation | VMnet   | Port  |
|-------------------|-------------|---------|-------|
| 10.0.2.0/24       | LAN Lille   | VMnet10 | ens33 |
| 192.168.58.101/24 | NAT         | NAT     | ens37 |

## Carte réseau Boston
<img width="754" height="289" alt="image" src="https://github.com/user-attachments/assets/307cf633-ea73-4180-9f52-fbc02469445f" />

## Carte réseau Lille
<img width="751" height="303" alt="image" src="https://github.com/user-attachments/assets/ff794cb2-6c53-448e-90c0-7809a3a740b0" />

## Carte réseau ISP 
<img width="757" height="287" alt="image" src="https://github.com/user-attachments/assets/cf74b207-7cbb-4a72-8a33-fb843bd25fcf" />

## Carte réseau Client-Boston
<img width="753" height="258" alt="image" src="https://github.com/user-attachments/assets/ef46a8a7-2726-4cf1-af6a-3257d33d282b" />

## Carte réseau Client-Lille
<img width="754" height="257" alt="image" src="https://github.com/user-attachments/assets/8c173aa7-cab1-4223-80f1-34374103ca5c" />

## MOBAXTERM 
Connexion SSH 
<img width="1495" height="236" alt="image" src="https://github.com/user-attachments/assets/c7a9082d-2b0d-4b92-8cbb-d5ee14aded1a" />


## Configuration de base Vyos-Boston

#### Nom
- configure
- set system host-name Boston

#### Interfaces
- configure
- set interfaces ethernet eth0 address 192.168.58.128/24   # NAT
- set interfaces ethernet eth1 address 192.168.23.2/30     # vers ISP
- set interfaces ethernet eth2 address 172.16.0.2/30       # backbone interne
- set interfaces ethernet eth4 address 10.0.1.1/24         # LAN Boston

#### Route par défaut
- set protocols static route 0.0.0.0/0 next-hop 192.168.58.2

## Configuration de base Vyos-Lille

#### Nom
- configure
- set system host-name Lille

#### Interfaces
- configure
- set interfaces ethernet eth0 address 192.168.58.132/24   # NAT
- set interfaces ethernet eth1 address 192.168.12.2/30     # vers ISP
- set interfaces ethernet eth2 address 172.16.0.1/30       # backbone interne
- set interfaces ethernet eth3 address 10.0.2.1/24         # LAN Lille

#### Route par défaut
- set protocols static route 0.0.0.0/0 next-hop 192.168.58.2

## Configuration de base Vyos-ISP

#### Nom
- configure
- set system host-name ISP

#### Interfaces
- configure
- set interfaces ethernet eth1 address 192.168.58.131/24   # NAT / Internet
- set interfaces ethernet eth2 address 192.168.23.1/30     # vers Boston
- set interfaces ethernet eth3 address 192.168.12.1/30     # vers Lille

#### Route par défaut vers Internet réel
set protocols static route 0.0.0.0/0 next-hop 192.168.58.2

## Configuration Machine Boston

#### IP Statique
- sudo ip addr add 10.0.1.10/24 dev ens33
- sudo ip link set ens33 up
- sudo ip route add default via 10.0.1.1
  
#### NAT
- sudo ip addr add 192.168.58.100/24 dev ens37
- sudo ip link set ens37 up
  
#### DNS
- echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf
  
## Configuration Machine Lille

#### IP Statique
- sudo ip addr add 10.0.2.10/24 dev ens33
- sudo ip link set ens33 up
- sudo ip route add default via 10.0.2.1
  
#### NAT
- sudo ip addr add 192.168.58.100/24 dev ens37
- sudo ip link set ens37 up
  
#### DNS
- echo "nameserver 8.8.8.8" | sudo tee /etc/resolv.conf


## OPSF 

### VyOS Boston

#### Activation OSPF
- set protocols ospf parameters router-id 1.1.1.1

#### Backbone (area 0)
- set protocols ospf area 0 network 172.16.0.0/30

#### Area 1 (LAN Boston)
- set protocols ospf area 1 network 10.0.1.0/24

### VyOS Lille

#### Activation OSPF
- set protocols ospf parameters router-id 2.2.2.2

#### Backbone (area 0)
- set protocols ospf area 0 network 172.16.0.0/30

#### Area 2 (LAN Lille)
- set protocols ospf area 2 network 10.0.2.0/24

## BGP

### VyOS Boston

- set protocols bgp system-as 65001
- set protocols bgp parameters router-id 1.1.1.1

#### Voisin ISP
- set protocols bgp neighbor 192.168.23.1 remote-as 65000

#### Annonce du LAN Boston
- set protocols bgp address-family ipv4-unicast network 10.0.1.0/24

### VyOS Lille
- set protocols bgp system-as 65002
- set protocols bgp parameters router-id 2.2.2.2

#### Voisin ISP
- set protocols bgp neighbor 192.168.12.1 remote-as 65000

#### Annonce du LAN Lille
- set protocols bgp address-family ipv4-unicast network 10.0.2.0/24

### VyOS ISP
- set protocols bgp system-as 65000
- set protocols bgp parameters router-id 3.3.3.3

#### Voisin Boston
- set protocols bgp neighbor 192.168.23.2 remote-as 65001

#### Voisin Lille
- set protocols bgp neighbor 192.168.12.2 remote-as 65002


## Mise en place du serveur VPN sur Boston
### Configuration du serveur VPN sur Boston

#### Génération de la clé privée et du certificat
- openssl genrsa -out server-key.pem 2048
- openssl req -new -x509 -key server-key.pem -out server-cert.pem -days 365

### Installation du serveur OpenConnect
- sudo apt update
- sudo apt install ocserv -y

### Configuration du serveur OpenConnect
- sudo nano /etc/ocserv/ocserv.conf
  
tcp-port = 443
auth = "plain[passwd=/etc/ocserv/ocpasswd]"

server-cert = /etc/ocserv/server-cert.pem
server-key = /etc/ocserv/server-key.pem

ipv4-network = 10.10.10.0/24
route = 10.0.1.0/24
dns = 8.8.8.8 
### 1. Redirection de port sur ISP (NAT)

#### Redirection HTTPS (VPN OpenConnect) Vyos-ISP
- set nat destination rule 10 description 'VPN vers Boston'
- set nat destination rule 10 inbound-interface eth1
- set nat destination rule 10 protocol tcp
- set nat destination rule 10 destination port 443
- set nat destination rule 10 translation address 10.0.1.10
- set nat destination rule 10 translation port 443

## Installation Openconnect VPN sur Windows
<img width="1617" height="451" alt="image" src="https://github.com/user-attachments/assets/a3f84b2a-ec7a-4ca6-99ac-3f0b0b624e75" />

 
 

 




