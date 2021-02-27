# Voice-VLANs
How to configure IP phones PT
VOICE VLAN
============

Configure the ROS R1
=======================
en
conf t
int f0/0
no shut
int f0/0.10
encapsulation dot1q 10
ip add 192.168.10.1 255.255.255.0
exit
int f0/0.20
encapsulation dot1q 20 native
ip add 192.168.20.1 255.255.255.0
exit
int f0/0.50
encapsulation dot1q 50
ip add 192.168.50.1 255.255.255.0
exit

Configure SW1
=================
en
conf t
int f0/5
switchport mode trunk
switchport trunk native vlan 20
exit
vlan 10
name DATA
vlan 20 
name native
vlan 50
name voice
exit

On switch CLI Assign IPs to VLANs
==================================
int vlan 10
ip add 192.168.10.2 255.255.255.0
no shut
exit
ip default-gateway 192.168.10.1 
exit

Create Access ports
========================
int fa0/1
switchport mode Access
switchport Access vlan 10

int fa0/2
switchport mode Access
switchport voice vlan 50

int fa0/3
switchport mode Access
switchport Access vlan 10
switchport voice vlan 50

int fa0/4
switchport mode Access
switchport Access native vlan 20


On R1 exclude address on DHCP
===============================
ip dhcp excluded-address 192.168.10.1 192.168.10.20
ip dhcp excluded-address 192.168.50.1 192.168.50.20
ip dhcp excluded-address 192.168.20.1 192.168.20.20

Now Create DHCP pool
============================
ip dhcp pool DATA
network 192.168.10.0 255.255.255.0
default-router 192.168.10.1
exit
ip dhcp pool Voice
network 192.168.50.0 255.255.255.0
default-router 192.168.50.1
option 150 ip 192.168.50.1
exit


ip dhcp pool Native
network 192.168.20.0 255.255.255.0
default-router 192.168.20.1
exit

ON R1 Config Telephony -server
==================================
Telephony-service
max-ephone 5
max-dn 5
ip source-address 192.168.50.1 port 2001
auto assign 4 to 6
auto assign 1 to 5
exit
Telephony-service
ephone-dn 1
number 1975
ephone-dn 2
number 2003
ephone-dn 3
number 2004
ephone-dn 4
number 2006
ephone-dn 5
number 2009
