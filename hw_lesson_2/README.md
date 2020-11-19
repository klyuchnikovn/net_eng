# Настройка Router-on-a-Stick Inter-VLAN Routing

## Задание: 
#[Heading link](https://github.com/klyuchnikovn/net_eng/blob/master/hw_lesson_2/README.md#test "test")
  1. [Собрать сеть и произвести базовые настройки устройств](https://github.com/klyuchnikovn/net_eng/blob/master/hw_lesson_2/README.md#производим-предварительные-действия "Производим предварительные действия")
  2. [Создать VLAN'ы и назначить их на интерфейсы](https://github.com/klyuchnikovn/net_eng/blob/master/hw_lesson_2/README.md#создаём-vlan-и-svi "Создаём VLAN и SVI")
  3. [Настроить trunk'и между коммутаторами](https://github.com/klyuchnikovn/net_eng/blob/master/hw_lesson_2/README.md#настраиваем-на-свитчах-trunk "Настраиваем на свитчах trunk")
  4. [Настроить взаимодействие между VLAN на маршрутизаторе](https://github.com/klyuchnikovn/net_eng/blob/master/hw_lesson_2/README.md#настраиваем-на-роутере-адреса-и-подынтерфейсы "Настраиваем на роутере адреса и подынтерфейсы")
  5. [Проверить всё в работе](https://github.com/klyuchnikovn/net_eng/blob/master/hw_lesson_2/README.md#проверяем-с-пк "Проверяем с ПК")

## Топология: 
![](topology.PNG)

## Решение: 

### Производим предварительные действия

1. Задокументируем таблицы адресации и VLAN в рамках нашей топологии: 

- Таблица адресации: 
| Device | Interface | IP Address   | Subnet Mask   | Default Gateway |
|--------|-----------|--------------|---------------|-----------------|
| R1     | G0/0.3    | 192.168.3.1  | 255.255.255.0 | N/A             |
| R1     | G0/0.4    | 192.168.4.1  | 255.255.255.0 | N/A             |
| R1     | G0/0.8    | N/A          | N/A           | N/A             |
| S1     | VLAN 3    | 192.168.3.11 | 255.255.255.0 | 192.168.3.1     |
| S2     | VLAN 3    | 192.168.3.12 | 255.255.255.0 | 192.168.3.1     |
| PC-A   | NIC       | 192.168.3.3  | 255.255.255.0 | 192.168.3.1     |
| PC-B   | NIC       | 192.168.4.3  | 255.255.255.0 | 192.168.4.1     |
- Таблица VLAN: 
| VLAN |    Name    |            Interface Assigned           |
|:----:|:----------:|:---------------------------------------:|
| 3    | Management | S1: VLAN 3 S2: VLAN 3 S1: Gi0/2         |
| 4    | Operations | S2: Gi0/1                               |
| 7    | ParkingLot | S1: Gi0/3, Gi1/0-3 S2: Gi0/2-3, Gi1/0-3 |
| 8    | Native     | N/A                                     |

2. Произведём настройки по ТЗ в файле: 

-R1

```
Router>en    
Router#conf t
Router(config)# hos R1
R1(config)#no ip domain-lookup 
R1(config)#service password-encryption 
R1(config)#enab sec cisco
R1(config)#line con 0
R1(config-line)#password cisco
R1(config-line)#exit
R1(config)#usern cisco sec cisco
R1(config)#banner exec $ 
Enter TEXT message.  End with the character '$'.
***************************************
* Unauthorized access is prohibited!  *
***************************************
$
R1(config)#banner incoming $
Enter TEXT message.  End with the character '$'.
***************************************
* Unauthorized access is prohibited!  *
***************************************
$
R1(config)#banner login $
Enter TEXT message.  End with the character '$'.
***************************************
* Unauthorized access is prohibited!  *
***************************************
$
R1(config)#exit
R1#clock set 13:58:00 19 nov 2020 
R1#copy ru st
Destination filename [startup-config]? 
Building configuration...
[OK]
```

-S1 и S2
```
Настроены идентично маршрутизатору
```

-PC-A

```
VPCS> set pcname PC-A
PC-A> 
PC-A> ip 192.168.3.3/24 192.168.3.1                          
Checking for duplicate address...
PC1 : 192.168.3.3 255.255.255.0 gateway 192.168.3.1
PC-A>
```

-PC-B

```
VPCS> set pcname PC-B
PC-B> 
PC-B> ip 192.168.4.3/24 192.168.4.1 
Checking for duplicate address...
PC1 : 192.168.4.3 255.255.255.0 gateway 192.168.4.1
PC-B> 
```

### Создаём VLAN и SVI

-S1

```
S1(config)#vlan 3
S1(config-vlan)#name Management
S1(config-vlan)#vlan 4
S1(config-vlan)#name Operations
S1(config-vlan)#vlan 7 
S1(config-vlan)#name ParkingLot
S1(config-vlan)#vlan 8
S1(config-vlan)#name Native
S1(config-vlan)#exit
S1(config)#int vlan3
S1(config-if)#ip add 192.168.3.11 255.255.255.0
S1(config-if)#no sh
S1(config-if)#exit    
S1(config)#ip default-gateway 192.168.3.1
S1(config)#int gi0/2
S1(config-if)#sw m a
S1(config-if)#sw ac vl 3
S1(config)#int gi0/3 
S1(config-if-range)#sw m a
S1(config-if-range)#sw a v 7
S1(config-if-range)#shut
S1(config-if-range)#int ra gi1/0-3
S1(config-if-range)#sw m a
S1(config-if-range)#sw a v 7
S1(config-if-range)#shut
```

-S2

```
S2(config)#vlan 3
S2(config-vlan)#name Management
S2(config-vlan)#vlan 4
S2(config-vlan)#name Operations
S2(config-vlan)#vlan 7 
S2(config-vlan)#name ParkingLot
S2(config-vlan)#vlan 8
S2(config-vlan)#name Native
S2(config-vlan)#exit
S2(config)#int vlan3
S2(config-if)#ip add 192.168.3.12 255.255.255.0
S2(config-if)#no sh
S2(config-if)#exit    
S2(config)#ip default-gateway 192.168.3.1
S2(config)#int gi0/1
S2(config-if)#sw m a
S2(config-if)#sw a v 4
S2(config)#int ra gi0/2-3 
S2(config-if-range)#sw m a
S2(config-if-range)#sw a v 7
S2(config-if-range)#shut
S2(config-if-range)#int ra gi1/0-3
S2(config-if-range)#sw m a
S2(config-if-range)#sw a v 7
S2(config-if-range)#shut
```

### Настраиваем на свитчах trunk

-S1

```
S1(config-if)#int gi0/0
S1(config-if)#sw tr enc dot1q
S1(config-if)#sw m t
S1(config-if)#sw tr all vl 3,4,8
S1(config-if)#sw tr na vl 8
S1(config-if)#int gi0/1
S1(config-if)#sw tr enc dot1q
S1(config-if)#sw m t
S1(config-if)#sw tr all vl 3,4,8
S1(config-if)#sw tr na vl 8
```

-S2

```
S2(config)#int gi0/0
S2(config-if)#sw tr enc dot1q
S2(config-if)#sw m t
S2(config-if)#sw tr all vl 3,4,8
S2(config-if)#sw tr na vl 8
```

### Настраиваем на роутере адреса и подынтерфейсы

-R1

```
R1(config)#int gi0/0
R1(config-if)#no sh
R1(config)#int gi0/0.3
R1(config-subif)#enc dot1q 3
R1(config-subif)#des Management
R1(config-subif)#ip add 192.168.3.1 255.255.255.0
R1(config-subif)#int gi0/0.4
R1(config-subif)#des Operations
R1(config-subif)#enc dot1q 4
R1(config-subif)#ip add 192.168.4.1 255.255.255.0
R1(config-subif)#int gi0/0.8
R1(config-subif)#des Native
R1(config-subif)#enc dot1q 8
```

### Проверяем с ПК

-С PC-A по условию нужно проверить доступность шлюза по-умолчанию, PC-B и управления S2

```
PC-A> ping 192.168.3.1
84 bytes from 192.168.3.1 icmp_seq=1 ttl=255 time=2.205 ms
84 bytes from 192.168.3.1 icmp_seq=2 ttl=255 time=1.802 ms

PC-A> ping 192.168.4.3
84 bytes from 192.168.4.3 icmp_seq=1 ttl=63 time=4.483 ms
84 bytes from 192.168.4.3 icmp_seq=2 ttl=63 time=3.159 ms

PC-A> ping 192.168.3.12
84 bytes from 192.168.3.12 icmp_seq=1 ttl=255 time=2.216 ms
84 bytes from 192.168.3.12 icmp_seq=2 ttl=255 time=2.049 ms
```

-С PC-B по условию нужно сделать трассировку до PC-A

```
PC-B> trace 192.168.3.3
trace to 192.168.3.3, 8 hops max, press Ctrl+C to stop
 1   192.168.4.1   3.067 ms  2.389 ms  2.768 ms
 2   *192.168.3.3   6.056 ms (ICMP type:3, code:3, Destination port unreachable)
```

### Готово
Все конфиги хранятся [здесь](configs/).