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

<style type="text/css">
.tg  {border-collapse:collapse;border-spacing:0;}
.tg td{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  overflow:hidden;padding:10px 5px;word-break:normal;}
.tg th{border-color:black;border-style:solid;border-width:1px;font-family:Arial, sans-serif;font-size:14px;
  font-weight:normal;overflow:hidden;padding:10px 5px;word-break:normal;}
.tg .tg-0pky{border-color:inherit;text-align:left;vertical-align:top}
</style>
<table class="tg">
<thead>
  <tr>
    <th class="tg-0pky">Device</th>
    <th class="tg-0pky">Interface</th>
    <th class="tg-0pky">IP Address</th>
    <th class="tg-0pky">Subnet Mask</th>
    <th class="tg-0pky">Default Gateway</th>
  </tr>
</thead>
<tbody>
  <tr>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
  </tr>
  <tr>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
  </tr>
  <tr>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
  </tr>
  <tr>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
  </tr>
  <tr>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
  </tr>
  <tr>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
    <td class="tg-0pky"></td>
  </tr>
</tbody>
</table>