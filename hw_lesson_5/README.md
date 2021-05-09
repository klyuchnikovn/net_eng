Начало положено? 
-----
Нарезаны сети. 

MSK: 
AS 1001
Nets: 
IPv4: 100.1.0.0/16
IPv6: 1001::/64
Nets MGMT: 
IPv4: 10.1.1.0/24
IPv6: fe80:1001::/64

SPB: 
AS 2042 
Nets: 
IPv4: 204.2.0.0/16
IPv6: 2042::/64
Nets MGMT: 
IPv4: 10.20.4.0/24
IPv6: fe80:2042::/64

LAM: 
AS 301
Nets: 
IPv4: 30.1.0.0/16
IPv6: 301::/64
Nets MGMT: 
IPv4: 10.30.1.0/24
IPv6: fe80:301::/64

KIT: 
AS 101
Nets: 
IPv4: 101.0.0.0/16
IPv6: 101::/64
Nets MGMT: 
IPv4: 10.101.1.0/24
IPv6: fe80:101::/64

TRI: 
AS 520
Nets: 
IPv4: 52.0.0.0/16
IPv6: 520::/64
Nets MGMT: 
IPv4: 
IPv6: 

LAB: 
AS 1001
Nets MSK
Nets MGMT MSK: 

CHO: 
AS 1001
Nets MSK
Nets MGMT MSK: 

По сети в целом: 
MGMT-VLAN всегда 199
ser-vlan всегда по номеру AS
Коды интерфейсов: 
e0/0: 5 
e0/1: 6
e0/2: 7
e0/3: 8
e1/0: 15
e1/1: 16
e1/2: 17
e1/3: 18

Даже если стык. 


Москва: 
Особенности сети: 
Клиенты получают IP по DHCP. Оба IPv4. 
DHCP-server: MSK-R5
MSK-R6: MGMT-server, с него доступ на остальную сеть

Шаблон конфига: 

conf t
hostname 
interface 0/0
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_
no shut
interface 0/1
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_
no shut
interface 0/2
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_
no shut
interface 0/3
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_
no shut

MSK-R1: 

conf t
hostname MSK-R1
interface 0/0
ip address 100.1.0.5 255.255.0.0
ipv6 address fe80:1001::/64 link-local
ipv6-address 1001::5/64
des link_to_MSK-R3
no shut
interface 0/1
ip address 100.1.0.6 255.255.0.0
ipv6 address fe80:1001::/64 link-local
ipv6-address 1001::6/64
des link_to_MSK-R2
no shut
interface 0/2
ip address 100.1.0.7 255.255.0.0
ipv6 address fe80:1001::/64 link-local
ipv6-address 1001::7/64
des link_to_KIT-R1
no shut
interface 0/3
ip address 100.1.0.8 255.255.0.0
ipv6 address IP link-local
ipv6-address 1001::8/64
des link_to_MSK-R5
no shut

MSK-R2: 

conf t
hostname MSK-R2
interface 0/0
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R4
no shut
interface 0/1
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R1
no shut
interface 0/2
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_LAM-R1
no shut
interface 0/3
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R6
no shut



MSK-R3: 

conf t
hostname MSK-R3
interface 0/0
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-SW1
no shut
interface 1/1
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-SW1
no shut
interface 0/1
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R4
no shut
interface 0/2
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R1
no shut


MSK-R4: 

conf t
hostname MSK-R4
interface 0/0
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-SW2
no shut
interface 1/1
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-SW2
no shut
interface 0/1
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R3
no shut
interface 0/2
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R2
no shut

MSK-R5: 

conf t
hostname MSK-R5
interface 0/0
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R1
no shut


MSK-R6: 

conf t
hostname MSK-R6
interface 0/0
ip address 
ipv6 address IP link-local
ipv6-address 
des link_to_MSK-R2
no shut

MSK-SW1: 

conf t
hostname MSK-SW1
interface e0/0
des link_to_MSK-SW3
interface e0/1
sw tr en dot1q
sw mode tr
sw tr all vl 1001, 199
des link_to_MSK-SW3
interface e0/2
sw tr en dot1q
sw mode tr
sw tr all vl 1001, 199
interface e0/3
sw tr en dot1q
sw mode tr
sw tr all vl 1001, 199
interface e1/0
sw tr en dot1q
sw mode tr
sw tr all vl 1001, 199
es link_to_MSK-R3
interface e1/1
sw tr en dot1q
sw mode tr
sw tr all vl 1001, 199
es link_to_MSK-R3
interface e1/2
sw tr en dot1q
sw mode tr
sw tr all vl 999
shut
interface e1/3
sw tr en dot1q
sw mode tr
sw tr all vl 999
shut