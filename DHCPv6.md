#  Настройка DHCPv6
## Задание:
1. [Создание сети и настройка основных параметров устройств](https://github.com/klyuchnikovn/net_eng/blob/master/DHCPv6.md#%D1%81%D0%BE%D0%B7%D0%B4%D0%B0%D0%BD%D0%B8%D0%B5-%D1%81%D0%B5%D1%82%D0%B8-%D0%B8-%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%BE%D1%81%D0%BD%D0%BE%D0%B2%D0%BD%D1%8B%D1%85-%D0%BF%D0%B0%D1%80%D0%B0%D0%BC%D0%B5%D1%82%D1%80%D0%BE%D0%B2-%D1%83%D1%81%D1%82%D1%80%D0%BE%D0%B9%D1%81%D1%82%D0%B2)
2. [Проверка назначения адреса SLAAC от R1](https://github.com/klyuchnikovn/net_eng/blob/master/DHCPv6.md#%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D0%BD%D0%B0%D0%B7%D0%BD%D0%B0%D1%87%D0%B5%D0%BD%D0%B8%D1%8F-%D0%B0%D0%B4%D1%80%D0%B5%D1%81%D0%B0-slaac-%D0%BE%D1%82-r1)
3. [Настройка и проверка сервера DHCPv6 без сохранения состояния на R1](https://github.com/klyuchnikovn/net_eng/blob/master/DHCPv6.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0-dhcpv6-%D0%B1%D0%B5%D0%B7-%D1%81%D0%BE%D1%85%D1%80%D0%B0%D0%BD%D0%B5%D0%BD%D0%B8%D1%8F-%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D1%8F-%D0%BD%D0%B0-r1)
4. [Настройка и проверка состояния DHCPv6 сервера на R1](https://github.com/klyuchnikovn/net_eng/blob/master/DHCPv6.md#%D0%BD%D0%B0%D1%81%D1%82%D1%80%D0%BE%D0%B9%D0%BA%D0%B0-%D0%B8-%D0%BF%D1%80%D0%BE%D0%B2%D0%B5%D1%80%D0%BA%D0%B0-%D1%81%D0%BE%D1%81%D1%82%D0%BE%D1%8F%D0%BD%D0%B8%D1%8F-dhcpv6-%D1%81%D0%B5%D1%80%D0%B2%D0%B5%D1%80%D0%B0-%D0%BD%D0%B0-r1)
5. Настройка и проверка DHCPv6 Relay на R2
## Топология:
![](dhcp6_topology.png)
## Решение: 
### Создание сети и настройка основных параметров устройств
####  Создадим схему адресации
- Таблица адресации: 


|  				Device 			 |  				Interface 			 |       				IPv6 Address 			      |
|:--------:|:-----------:|:------------------------:|
|  				R1 			     |  				G0/0/0 			    |  				2001:db8:acad:2::1 /64 			 |
|  				R1 			     |  				G0/0/0 			    |  				fe80::1 			                |
|  				R1 			     |  				G0/0/1 			    |  				2001:db8:acad:1::1/64 			  |
|  				R1 			     |  				G0/0/1 			    |  				fe80::1 			                |
|  				R2 			     |  				G0/0/0 			    |  				2001:db8:acad:2::2/64 			  |
|  				R2 			     |  				G0/0/0 			    |  				fe80::2 			                |
|  				R2 			     |  				G0/0/1 			    |  				2001:db8:acad:3::1 /64 			 |
|  				R2 			     |  				G0/0/1 			    |  				fe80::1 			                |
|  				PC-A 			   |  				NIC 			       |  				DHCP 			                   |
|  				PC-B 			   |  				NIC 			       |  				DHCP 			                   |

#### Настроим коммутаторы
- S1
```
Switch>en
Switch#conf t
Switch(config)#host S1
S1(config)#no ip domain-lookup
S1(config)#service password-encryption 
S1(config)#ena sec class
S1(config)#line con 0
S1(config-line)#password cisco
S1(config-line)#logging synchronous 
S1(config-line)#login
S1(config-line)#line vty 0 4
S1(config-line)#password cisco
S1(config-line)#login
S1(config-line)#exit
S1(config)#banner #
Enter TEXT message.  End with the character '#'.
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
!!      Only authorized access!      !!   
!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!!
#
S1(config)#end
S1#clock set 14:35:00 28 Mar 2021
S1#copy run start
Destination filename [startup-config]? 
Building configuration...
[OK]
S1#
```
- S2
```
Настройки выполнены аналогично
``` 

#### Настроим маршрутизаторы
- R1
```
Router>en
Router#conf t
Router(config)#ipv6 unicast-routing
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
#### Настроим интерфейсы и маршрутизацию для обоих маршрутизаторов
- R1
```
R1(config)#int gi0/0
R1(config-if)#des R2
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#ipv6 address 2001:db8:acad:2::1/64 
R1(config-if)#no shutdown
R1(config-if)#int gi0/1
R1(config-if)#des S1   
R1(config-if)#ipv6 address fe80::1 link-local
R1(config-if)#ipv6 address 2001:db8:acad:1::1/64
R1(config-if)#no shutdown
R1(config-if)#ipv6 route ::/0 2001:DB8:ACAD:2::2
R1(config)#do copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R1(config)#
```
- R2
```
R2(config)#int gi0/0
R2(config-if)#des R1
R2(config-if)#ipv6 address fe80::1 link-local
R2(config-if)#ipv6 address 2001:db8:acad:2::2/64
R2(config-if)#no shutdown
R2(config-if)#int gi0/1
R2(config-if)#des S2  
R2(config-if)#ipv6 address fe80::1 link-local
R2(config-if)#ipv6 address 2001:db8:acad:3::1/64 
R2(config-if)#no shutdown
R2(config-if)#ipv6 route ::/0 2001:DB8:ACAD:2::1
R2(config)#do copy running-config startup-config
Destination filename [startup-config]? 
Building configuration...
[OK]
R2(config)#
```
#### Проверим работу маршрутизации
```
R1#ping 2001:DB8:ACAD:2::2
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 2001:DB8:ACAD:2::2, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 1/1/2 ms
R1#
```
### Проверка назначения адреса SLAAC от R1
```
VPCS> set pcname PC-A

PC-A> 

VPCS> ip auto  
GLOBAL SCOPE      : 2001:db8:acad:1:2050:79ff:fe66:680b/64
ROUTER LINK-LAYER : 50:00:00:07:00:01

PC-A> ping 2001:db8:acad:1::1

2001:db8:acad:1::1 icmp6_seq=1 ttl=64 time=2.374 ms
2001:db8:acad:1::1 icmp6_seq=2 ttl=64 time=2.195 ms
2001:db8:acad:1::1 icmp6_seq=3 ttl=64 time=2.362 ms
2001:db8:acad:1::1 icmp6_seq=4 ttl=64 time=2.267 ms
PC-A>
```
### Настройка и проверка сервера DHCPv6 без сохранения состояния на R1
#### Настроим R1 
```
R1#conf t
R1(config)#ipv6 dhcp pool R1-STATELESS
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATELESS.com
R1(config-dhcpv6)#interface GigabitEthernet0/1
R1(config-if)#ipv6 nd other-config-flag
R1(config-if)#ipv6 dhcp server R1-STATELESS
R1(config-if)#do copy run start
Destination filename [startup-config]? 
Building configuration...
[OK]
R1#
```
#### Перезапустим PC-A и проверим подключение
```

```
### Настройка и проверка состояния DHCPv6 сервера на R1
#### Проверим на PC-B адрес SLAAC, который он генерирует
```
PC-B> show ipv6

NAME              : VPCS[1]
LINK-LOCAL SCOPE  : fe80::250:79ff:fe66:680c/64
GLOBAL SCOPE      : 2001:db8:acad:3:2050:79ff:fe66:680c/64
DNS               :
ROUTER LINK-LAYER : 50:00:00:08:00:01
MAC               : 00:50:79:66:68:0c
LPORT             : 20000
RHOST:PORT        : 127.0.0.1:30000
MTU:              : 1500

PC-B>
```
#### Настроим R2 в качестве агента DHCP relay
```
R1(config)#ipv6 dhcp pool R2-STATEFUL
R1(config-dhcpv6)#address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcpv6)#dns-server 2001:db8:acad::254
R1(config-dhcpv6)#domain-name STATEFUL.com
R1(config-dhcpv6)#int gi0/0
R1(config-if)#ipv6 dhcp server R2-STATEFUL
R1(config-if)#
```
#### Перезапустим PC-B и проверим подключение
