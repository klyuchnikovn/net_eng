# Настройка Router-on-a-Stick Inter-VLAN Routing

## Задание: 
  1. Собрать сеть и произвести базовые настройки устройств
  2. Создать VLAN'ы и назначить их на интерфейсы
  3. Настроить trunk'и между коммутаторами
  4. Настроить взаимодействие между VLAN на маршрутизаторе
  5. Проверить всё в работе

## Топология: 

## Решение: 

  1. [Задокументируем таблицу адресации;]




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

Коммутаторы S1 и S2 настроены идентично, согласно заданию. 

Настройка PC-A: 

```
VPCS> set pcname PC-A
PC-A> 
PC-A> ip 192.168.3.3/24 192.168.3.1                          
Checking for duplicate address...
PC1 : 192.168.3.3 255.255.255.0 gateway 192.168.3.1
PC-A>
```

Настройка PC-B: 

```
VPCS> set pcname PC-B
PC-B> 
PC-B> ip 192.168.4.3/24 192.168.4.1 
Checking for duplicate address...
PC1 : 192.168.4.3 255.255.255.0 gateway 192.168.4.1
PC-B> 
```

<thead>
  <tr>
    <th>Device</th>
    <th>Interface</th>
    <th>IP Address</th>
    <th>Subnet Mask</th>
    <th>Default Gateway</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td>R1</td>
    <td>G0/0/1.3</td>
    <td>192.168.3.1</td>
    <td>255.255.255.0</td>
    <td rowspan="3">N/A</td>
  </tr>
  <tr>
    <td></td>
    <td>G0/0/1.4</td>
    <td>192.168.4.1</td>
    <td>255.255.255.0</td>
  </tr>
  <tr>
    <td></td>
    <td>G0/0/1.8</td>
    <td></td>
    <td></td>
  </tr>
  <tr>
    <td>S1</td>
    <td>VLAN 3</td>
    <td>192.168.3.11</td>
    <td>255.255.255.0</td>
    <td>192.168.3.1</td>
  </tr>
  <tr>
    <td>S2</td>
    <td>VLAN 3</td>
    <td>192.168.3.12</td>
    <td>255.255.255.0</td>
    <td>192.168.3.1</td>
  </tr>
  <tr>
    <td>PC-A</td>
    <td>NIC</td>
    <td>192.168.3.3</td>
    <td>255.255.255.0</td>
    <td>192.168.3.1</td>
  </tr>
  <tr>
    <td>PC-B</td>
    <td>NIC</td>
    <td>192.168.4.3</td>
    <td>255.255.255.0</td>
    <td>192.168.4.1</td>
  </tr>
</tbody>
</table>