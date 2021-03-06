#  Реализация DHCPv4
## Задание:
1.  [Создание сети и настройка основных параметров устройства](https://github.com/klyuchnikovn/net_eng/blob/master/DHCPv4.md#%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-%D1%81%D0%B5%D1%82%D0%B8-%D0%B8-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%BE%D1%81%D0%BD%D0%BE%D0%B2%D0%BD%D1%8B%D1%85-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D0%BE%D0%B2-%D1%83%D1%81%D1%82%D1%80%D0%BE%D0%B9%D1%81%D1%82%D0%B2%D0%B0)
2.  [Настройка и проверка двух серверов DHCPv4 на R1](https://github.com/klyuchnikovn/net_eng/blob/master/DHCPv4.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D0%B4%D0%B2%D1%83%D1%85-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%BE%D0%B2-dhcpv4-%D0%BD%D0%B0-r1)
3.  [Настройка и проверка DHCP Relay на R2](https://github.com/klyuchnikovn/net_eng/blob/master/DHCPv4.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-dhcp-relay-%D0%BD%D0%B0-r2)
## Топология:
![](dhcp_topology.png)
## Решение: 
### Создание сети и настройка основных параметров устройства
####  Создаём схему адресации
- Таблица адресации: 

|  				Device 			 |   				Interface 			  |  				IP Address 			 |    				Subnet Mask 			   |  				Default Gateway 			 |
|:--------:|:-------------:|:------------:|:-----------------:|:-----------------:|
|  				R1 			     |  				G0/0 			      |  				10.0.0.1 			   |  				255.255.255.252 			 |  				N/A 			             |
|  				R1 			     |  				G0/1 			      |  				N/A 			        |  				N/A 			             |  				N/A 			             |
|  				R1 			     |  				G0/1.100 			  | 192.168.1.1  | 255.255.255.192   |  				N/A 			             |
|  				R1 			     |  				G0/1.200 			  | 192.168.1.65 | 255.255.255.224   |  				N/A 			             |
|  				R1 			     |  				G0/1.1000 			 |  				N/A 			        |  				N/A 			             |  				N/A 			             |
|  				R2 			     |  				G0/0 			      |  				10.0.0.2 			   |  				255.255.255.252 			 |  				N/A 			             |
|  				R2 			     |  				G0/1 			      | 192.168.1.97 | 255.255.255.240   |  				N/A 			             |
|  				S1 			     |  				VLAN 200 			    | 192.168.1.66 | 255.255.255.224   | 192.168.1.65      |
|  				S2 			     |  				VLAN 1 			      | 192.168.1.98 | 255.255.255.240   | 192.168.1.97      |
|  				PC-A 			   |  				NIC 			         |  				DHCP 			       |  				DHCP 			            |  				DHCP 			            |
|  				PC-B 			   |  				NIC 			         |  				DHCP 			       |  				DHCP 			            |  				DHCP 			            |

- Таблица VLAN:

|  					VLAN 				 |      					Name 				    |  					Interface Assigned 				 |
|:------:|:-------------:|:--------------------:|
|  					1 				    |  					N/A 				         |  					S2: G0/1 				          |
|  					100 				  |  					Clients 				     |  					S1: G0/1 				          |
|  					200 				  |  					Management 				  |  					S1: VLAN 200  					 				     |
|  					999 				  |  					Parking_Lot 				 |  					S1: Gi0/2-3, Gi1/0-3			   |
|  					1000 				 |  					Native 				      |  					N/A 				                |


#### Настраиваем маршрутизаторы

- R1
```
Router>en
Router#conf t
Router(config)#host R1
R1(config)#no ip domain-lookup
R1(config)#service password-encryption 
R1(config)#ena sec class
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#logging synchronous 
R1(config-line)#login
R1(config-line)#line vty 0 4
R1(config-line)#password cisco
R1(config-line)#login
R1(config-line)#exit
R1(config)#banner #
Enter TEXT message.  End with the character '#'.
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!      Only authorized access!      !!   
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
#
R1(config)#end
R1#clock set 00:05:00 29 Mar 2021
R1#copy run start
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
- R2
```
Настройки выполнены аналогично
```
#### Настраиваем на роутерах адреса и подынтерфейсы
- R1
```
R1(config)#int gi0/1 
R1(config-if)#no sh
R1(config-if)#des S1
R1(config-subif)#int gi0/1.100
R1(config-subif)#des Clients
R1(config-subif)#enc dot1q 100
R1(config-subif)#ip add 192.168.1.1 255.255.255.192
R1(config-subif)#int gi0/1.200
R1(config-subif)#des Mgmt
R1(config-subif)#enc dot1q 200
R1(config-subif)#ip add 192.168.1.65 255.255.255.224
R1(config-subif)#int gi0/1.1000                     
R1(config-subif)#des Dummy_native
R1(config-subif)#int gi0/0
R1(config-if)#des R2
R1(config-if)#ip add 10.0.0.1 255.255.255.252
R1(config-if)#no sh
R1(config-if)#exit
R1(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.2 
R1(config)#
```
- R2
```
R2(config)#int gi0/1 
R2(config-if)#no sh
R2(config-if)#des S2
R2(config-if)#ip add 192.168.1.97 255.255.255.240
R2(config-if)#int gi0/0
R2(config-if)#no sh
R2(config-if)#des R1
R2(config-if)#ip add 10.0.0.2 255.255.255.252
R2(config-if)#exit
R2(config)#ip route 0.0.0.0 0.0.0.0 10.0.0.1
R2(config)#
```
#### Проверяем работу маршрутизации
```
R1#ping 192.168.1.97
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 192.168.1.97, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 2/2/2 ms
R1#
```
#### Настраиваем коммутаторы
- S1 и S2
```
Настроены идентично маршрутизаторам
```
#### Создаём VLAN и SVI
- S1
```
S1(config)#vlan 100
S1(config-vlan)#name Clients
S1(config-vlan)#vlan 200
S1(config-vlan)#name Mgmt
S1(config-vlan)#vlan 999
S1(config-vlan)#name Parking_lot
S1(config-vlan)#vlan 1000
S1(config-vlan)#name Native
S1(config-if)#int vl200
S1(config-if)#ip add 192.168.1.66 255.255.255.224
S1(config-if)#des Mgmt
S1(config-if)#no sh 
S1(config-if)#exit
S1(config)#ip default-gateway 192.168.1.65
S1(config)#do copy run start
Destination filename [startup-config]? 
Building configuration...
Compressed configuration from 3619 bytes to 1820 bytes[OK]
S1(config)#int ra gi0/2-3,gi1/0-3
S1(config-if-range)#sw acc vl 999
S1(config-if-range)#sh
```
- S2
```
S2(config)#ip default-gateway 192.168.1.97
S2(config)#vlan 999
S2(config-vlan)#name Parking_lot
S2(config-vlan)#int gi0/1
S2(config-if)#des PC-B
S2(config-if)#sw acc vl 1     
S2(config-if)#int vlan1
S2(config-if)#ip add 192.168.1.98 255.255.255.240
S2(config-if)#des Mgmt
S2(config-if)#no sh
S2(config-if)#int gi0/0
S2(config-if)#des R2
S2(config-if)#int ra gi0/2-3,gi1/0-3
S2(config-if-range)#sw acc vl 999
S2(config-if-range)#sh
```
#### Настраиваем на S1 trunk

```
S1(config-vlan)#int gi0/0 
S1(config-if)#des R1
S1(config-if)#sw tr enc dot1q
S1(config-if)#sw mo tr
S1(config-if)#sw tr al vl 100,200,1000
S1(config-if)#sw tr nat vl 1000
S1(config-if)#do sh run int gi0/0 
Building configuration...

Current configuration : 227 bytes
!
interface GigabitEthernet0/0
 description R1
 switchport trunk allowed vlan 100,200,1000
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 1000
 switchport mode trunk
 media-type rj45
 negotiation auto
end

S1(config-if)#int gi0/1
S1(config-if)#des PC-A
S1(config-if)#sw acc vl 100
S1(config-if)#do sh run int gi0/1
Building configuration...

Current configuration : 117 bytes
!
interface GigabitEthernet0/1
 description PC-A
 switchport access vlan 100
 media-type rj45
 negotiation auto
end
```
### Настройка и проверка двух серверов DHCPv4 на R1
#### Настраиваем на R1 пулы
```
R1(config)#ip dhcp excluded-address 192.168.1.1 192.168.1.5
R1(config)#ip dhcp pool R1_Client_LAN 
R1(dhcp-config)#network 192.168.1.0 255.255.255.192
R1(dhcp-config)#domain-name ccna-lab.com
R1(dhcp-config)#default-router 192.168.1.1
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#ip dhcp excluded-address 192.168.1.97 192.168.1.101
R1(config)#ip dhcp pool R2_Client_LAN
R1(dhcp-config)#network 192.168.1.96 255.255.255.240
R1(dhcp-config)#default-router 192.168.1.97
R1(dhcp-config)#domain-name ccna-lab.com     
R1(dhcp-config)#lease 2 12 30
R1(dhcp-config)#do wr
```
#### Проверяем конфигурацию сервера DHCPv4

#### Проверяем получение IP-адреса от DHCP на PC-A
```
VPCS> set pcname PC-A

PC-A> ip dhcp -r
DDORA IP 192.168.1.6/26 GW 192.168.1.1

PC-A> show ip

NAME        : PC-A[1]
IP/MASK     : 192.168.1.6/26
GATEWAY     : 192.168.1.1
DNS         : 
DHCP SERVER : 192.168.1.1
DHCP LEASE  : 217798, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:01
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500
```

### Настройка и проверка DHCP Relay на R2
#### Настраиваем R2 в качестве агента DHCP Relay 
```
R2(config)#interface GigabitEthernet0/1
R2(config-if)#ip helper-address 10.0.0.1
R2(config-if)#
```
#### Проверяем получение IP-адреса от DHCP на PC-В
```
VPCS> set pcname PC-B

PC-B> ip dhcp -r
DDORA IP 192.168.1.102/28 GW 192.168.1.97

PC-B> show ip

NAME        : PC-B[1]
IP/MASK     : 192.168.1.102/28
GATEWAY     : 192.168.1.97
DNS         :
DHCP SERVER : 10.0.0.1
DHCP LEASE  : 217792, 217800/108900/190575
DOMAIN NAME : ccna-lab.com
MAC         : 00:50:79:66:68:02
LPORT       : 20000
RHOST:PORT  : 127.0.0.1:30000
MTU         : 1500

PC-B>
```
#### Проверяем назначение адресов в DHCP
```
R1#show ip dhcp pool

Pool R1_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 62
 Leased addresses               : 1
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.7          192.168.1.1      - 192.168.1.62      1

Pool R2_Client_LAN :
 Utilization mark (high/low)    : 100 / 0
 Subnet size (first/next)       : 0 / 0 
 Total addresses                : 14
 Leased addresses               : 1
 Pending event                  : none
 1 subnet is currently in the pool :
 Current index        IP address range                    Leased addresses
 192.168.1.103        192.168.1.97     - 192.168.1.110     1
R1#show ip dhcp binding
Bindings from all pools not associated with VRF:
IP address          Client-ID/              Lease expiration        Type
                    Hardware address/
                    User name
192.168.1.6         0100.5079.6668.01       Mar 31 2021 10:20 AM    Automatic
192.168.1.102       0100.5079.6668.02       Mar 31 2021 10:25 AM    Automatic
R1#
```
