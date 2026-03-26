# Project VYOS

## Récapitulatif globale de l'adressage



## Topologie de l'infrastructure

<img width="771" height="775" alt="image" src="https://github.com/user-attachments/assets/bc13c892-4fbf-47b2-aab1-7ddb317a726c" />

Configuration de R2

## Ajout de 3 cartes réseaux dans la VYOS-R2

Network adaptater "eth0" --> Vlan11-RBORDER
Network adaptater 1 "eth1" --> Vlan12-R1
Network adaptater 2 "eth2" --> Vlan 14-CLients

<img width="760" height="735" alt="image" src="https://github.com/user-attachments/assets/f6cbcce0-68de-4ad6-bb75-e41af837c45e" />

# Mise en place des vlan
 
### Interface vers R-Border (VLAN 11)
set interfaces ethernet eth0 vif 11 address '10.1.11.2/30' 
set interfaces ethernet eth0 vif 11 description 'VLAN 11 - Vers R-Border' 
set interfaces ethernet eth0 vif 11 mtu '1500' 
 
### Interface vers R1 (VLAN 12)
set interfaces ethernet eth1 vif 12 address '10.1.12.2/30'
set interfaces ethernet eth1 vif 12 description 'VLAN 12 - Vers R1'
set interfaces ethernet eth1 vif 12 mtu '1500'
 
### Interface vers Clients (VLAN 14)
set interfaces ethernet eth2 vif 14 address '10.1.14.1/24'
set interfaces ethernet eth2 vif 14 description 'VLAN 14 - Sous-réseau Clients'
set interfaces ethernet eth2 vif 14 mtu '1500'

# Mise en place du loopback 
set interfaces loopback lo address '10.1.0.3/32'
set interfaces loopback lo description 'Loopback Router-ID'

# Mise en place OSPF

set protocols ospf parameters router-id '10.1.0.3'
set protocols ospf parameters abr-type 'cisco'
set protocols ospf log-adjacency-changes
 
 Area 0
set protocols ospf area 0 network '10.1.11.0/30'
set protocols ospf area 0 network '10.1.12.0/30'
set protocols ospf area 0 network '10.1.14.0/24'
set protocols ospf area 0 network '10.1.0.3/32'


 
 

 

