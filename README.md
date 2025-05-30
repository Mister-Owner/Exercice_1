port 8 - administration - vlan 30
ports 6-7 - PC fixes - vlan 20
ports 4-5 - Points d'accès Wi-Fi - vlan 10
ports 2-3 - Téléphones IP (VoIP) - vlan 1
ports 1 et 9 - Uplink (Ethernet / Fibre) - trunk

--------------------------------------------------------
*** Configuration des VLANs

vlan 1
 name VoIP

vlan 10
 name PC_Fixes

vlan 20
 name WiFi

vlan 30
 name Administration




*** Affectation de l'adresse IP pour le VLAN d'administration

interface vlan 30
 ip address 192.168.30.254 255.255.255.0
 no shutdown

pour les swithcs d'acces (251 à 253)

int vlan 1
no sh

int vlan 10
no sh

int vlan 20
no sh

int vlan 30
no sh

*** Configuration des ports selon leur usage

#Ports 2-3 : Téléphones IP (VoIP) - VLAN 1

interface fa2/1
 switchport mode access
 switchport access vlan 1
interface fa3/1
 switchport mode access
 switchport access vlan 1


#Ports 4-5 : Points d'accès Wi-Fi - VLAN 10

interface fa4/1
 switchport mode access
 switchport access vlan 10
interface fa5/1
 switchport mode access
 switchport access vlan 10


#Ports 6-7 : PC fixes - VLAN 20

interface fa6/1
 switchport mode access
 switchport access vlan 20
interface fa7/1
 switchport mode access
 switchport access vlan 20


#Port 8 : Administration - VLAN 30

interface fa8/1
 switchport mode access
 switchport access vlan 30



#Ports 1 et 9 : Uplink - Trunk

interface range fa1/1, fa9/1
 switchport mode trunk
 switchport trunk allowed vlan 1,10,20,30




-----------------------------------------------------------------
------------ INTERFACES TRUNKES VERS LES SWITCHES ------------

interface g0/0
 no shutdown
 ip nat inside


interface g0/0.1
 encapsulation dot1Q 1
 ip address 192.168.0.1 255.255.255.0
 ip nat inside
 ip helper-address 192.168.0.1

interface g0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
 ip nat inside
 ip helper-address 192.168.10.1


interface g0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
 ip nat inside
 ip helper-address 192.168.20.1


interface g0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
 ip nat inside
 ip helper-address 192.168.30.1




 ------------ INTERFACE LOOPBACK POUR INTERNET (NAT OUTSIDE) ------------
interface loopback0
 ip address 8.8.8.8 255.255.255.255




 ------------ MARQUER NAT INSIDE / OUTSIDE ------------



interface loopback0
 ip nat outside








 ------------ DHCP CONFIGURATION POUR CHAQUE VLAN ------------

ip dhcp excluded-address 192.168.0.1 192.168.0.9
ip dhcp excluded-address 192.168.10.1 192.168.10.9
ip dhcp excluded-address 192.168.20.1 192.168.20.9
ip dhcp excluded-address 192.168.30.1 192.168.30.9



#VLAN 1 - VoIP

ip dhcp pool VLAN1
 network 192.168.0.0 255.255.255.0
 default-router 192.168.0.1
 dns-server 8.8.8.8


#VLAN 10 - PC fixes

ip dhcp pool VLAN10
 network 192.168.10.0 255.255.255.0
 default-router 192.168.10.1
 dns-server 8.8.8.8



#VLAN 20 - Wi-Fi

ip dhcp pool VLAN20
 network 192.168.20.0 255.255.255.0
 default-router 192.168.20.1
 dns-server 8.8.8.8


# VLAN 30 - Administration

ip dhcp pool VLAN30
 network 192.168.30.0 255.255.255.0
 default-router 192.168.30.1
 dns-server 8.8.8.8





------------ NAT : OVERLOAD MULTI-LAN VERS INTERNET ------------

access-list 1 permit any

ip nat inside source 1 interface loopback0 overload


ip route 0.0.0.0 0.0.0.0 loopback0
