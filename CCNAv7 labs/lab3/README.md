# Лабораторная работа настройка DHCPv4

Топология

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/Топология.png)

#### Таблица адресации

| Device | Interface | IP Address | Subnet Mask     | Default Gateway |
| ------ | --------- | ---------- | --------------- | --------------- |
| R1     | e0/0      | 10.0.0.1   | 255.255.255.252 | N/A             |
| R1     | e0/1      | N/A        | N/A             |                 |
| R1     | e0/1.100  |            |                 |                 |
| R1     | e0/1.200  |            |                 |                 |
| R1     | e0/1.1000 | N/A        | N/A             |                 |
| R2     | e0/0      | 10.0.0.2   | 255.255.255.252 | N/A             |
| R2     | e0/1      |            |                 |                 |
| S1     | VLAN 200  |            |                 |                 |
| S2     | VLAN 1    |            |                 |                 |
| PC-S1  | NIC       | DHCP       | DHCP            | DHCP            |
| PC-S2  | NIC       | DHCP       | DHCP            | DHCP            |


#### VLAN Таблица

| VLAN | Name       | Interface Assigned |
| ---- | ---------- | ------------------ |
| 1    | N/A        | S2: e0/0           |
| 100  | Clients    | S1: e0/0           |
| 200  | Management | S1: VLAN 200       |
| 999  | ParkingLot | S1:e0/2-3          |
| 1000 | Native     | N/A                |

#### Содержание

1. Создать сеть и произвести базовую настройку устройств
2. Сконфигурировать и проверить два DHCPv4 сервера на R1
3. Сконфигурировать и проверить DHCP Relay на R2

## Создать сеть и произвести базовую настройку устройств

Разделить адрес 192.168.1.0/24 на 3 подсети 

1. Первая подсеть "Подсеть А" на 58 хостов (client VLAN at R1)
   Подсеть А:
   Количество пользователей - 58

   Network Address - 192.168.1.0

   Первый адрес - 192.168.1.1

   Последний адрес - 192.168.1.62

   Broadcast адрес - 192.168.1.63

   Маска подсети - 255.255.255.192

   Запишем первый IP-адрес в таблице адресации для R1 e0/1.100. Запишем второй IP-адрес в таблице адресов для S1 VLAN 200 и введем соответствующий шлюз по умолчанию.
   
2. Вторая подсеть "Подсеть B" на 28 хостов (Management VLAN at R1)

   Подсеть B:

   Количество пользователей - 28

   Network Address - 192.168.1.64

   Первый адрес - 192.168.1.65

   Последний адрес - 192.168.1.94

   Broadcast адрес - 192.168.1.95

   Маска подсети  255.255.255.224

   Запишем первый IP-адрес в таблицу адресации для R1 e0/1.200. Запишем второй IP-адрес в таблице адресов для S1 VLAN 1 и введем соответствующий шлюз по умолчанию.

3. Третья подсеть "Подсеть C" на 12 хостов (the client network at R2)

   Подсеть C:

   Количество пользователей - 12

   Network Address - 192.168.1.96

   Первый адрес - 192.168.1.97

   Последний адрес - 192.168.1.110

   Broadcast адрес - 192.168.1.111

   Маска подсети - 255.255.255.240
   
   Запишем первый IP-адрес в таблице адресации для R2 e0/1.

Готовый вариант.

| Device | Interface | IP Address       | Subnet Mask         | Default Gateway  |
| ------ | --------- | ---------------- | ------------------- | ---------------- |
| R1     | e0/0      | 10.0.0.1         | 255.255.255.252     | N/A              |
| R1     | e0/1      | N/A              | N/A                 |                  |
| R1     | e0/1.100  | **192.168.1.1**  | **255.255.255.192** |                  |
| R1     | e0/1.200  | **192.168.1.65** | **255.255.255.224** |                  |
| R1     | e0/1.1000 | N/A              | N/A                 | N/A              |
| R2     | e0/0      | 10.0.0.2         | 255.255.255.252     |                  |
| R2     | e0/1      | **192.168.1.97** | **255.255.255.240** |                  |
| S1     | VLAN 200  | **192.168.1.66** | **255.255.255.224** | **192.168.1.65** |
| S2     | VLAN 1    | **192.168.1.98** | **255.255.255.240** | **192.168.1.97** |
| PC-S1  | NIC       | DHCP             | DHCP                | DHCP             |
| PC-S2  | NIC       | DHCP             | DHCP                | DHCP             |

## 1: Построить сеть и произвести базовую настройку

Пример базовой настройки 

```
no ip domain-lookup
hostname R2
enable secret class
line console 0
	password cisco
	login
	logging synchronous
	exit
line vty 0 15
	password cisco
	login
	exit
service password-encryption
banner motd "Unauthorizes access is prohibited"
exit
clock set 14:00:00 09 July 2020
copy runnig-config startap-config
```

Настроим Inter-VLAN Маршрутизацию на R1

R1

```
interface e0/1
	no shut
	exit
interface e0/1.100
	encapsulation dot1q 100
	ip address 192.168.1.1 255.255.255.192
	des Client_Net
	exit
interface e0/1.200
	encapsulation dot1q 200
	ip address 192.168.1.65 255.255.255.224
	des Management_Net
	exit
interface e0/1.1000
	encapsulation dot1q 1000 native
	des Native_Net
	end
```

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20ip%20int%20b%20R1.png)

Настроим e0/1 на R2 и e0/0 затем настроим статическую маршрутизацию для обоих маршрутизаторов

R2

```
interface e0/1
	ip address 192.168.1.97 255.255.255.240
	no shut
	exit
interface e0/0
	ip address 10.0.0.2 255.255.255.252
	exit
ip route 0.0.0.0 0.0.0.0 10.0.0.1

```

R1

```
interface e0/0
	ip address 10.0.0.1 255.255.255.252
	no shut
	exit
ip route 0.0.0.0 0.0.0.0 10.0.0.2
```

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20ip%20r%20R1.png)

Проверим пингом
![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/ping%20192.168.1.97.png)

Создадим вланы на коммутаторах

S1

```
vlan 100
	name Clients
	vlan 200
	name Management 
	vlan 999 
	name Parking_Lot
	vlan 1000
	name Native
	exit
interface vlan 200
	ip address 192.168.1.66 255.255.255.224
	no shut
	exit
ip default-gateway 192.168.1.65
interface range e0/2 - 3
	switchport mode access
	switchport access vlan 999
	shutdown
	exit
```

S2

```
interface vlan 1
	ip address 192.168.1.98 255.255.255.240
	no shut
	exit
ip default-gateway 192.168.1.97
interface range e0/2 - 3
	switchport mode access
	switchport access vlan 999
	shutdown
	exit
```

Добавим вланы на порты и настроим как access

S1

```
interface e0/0
	switchport mode access
	switchport access vlan 100
	exit
```

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20vlan%20br%20S1.png)

На интерфейсе e0/1 будет влан 1 потому что мы не добавили ни один влан

Настроим 802.1Q на S1 интерфейсе e0/1

S1

```
interface e0/1
	switchport trunk encapsulation dot1q 
	switchport mode trunk
	switchport trunk native vlan 1000
	switchport trunk allowed vlan add 100,200,1000
	end
```

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20int%20trunk%20S1.png)

Первый ПК получил бы ip из подсети A

## 2: Сконфигурировать и проверить два DHCPv4 сервера на R1

R1

```
ip dhcp excluded-address 192.168.1.1 192.168.1.5
ip dhcp excluded-address 192.168.1.97 192.168.1.101
ip dhcp pool VLAN100
	network 192.168.1.0 255.255.255.192
	default-router 192.168.1.1
	domain-name ccna-lab.com
	lease 2 12 30
	exit
ip dhcp pool R2_Client_Lan
	network 192.168.1.96 255.255.255.240
	default-router 192.168.1.97
	domain-name ccna-lab.com
```

Посмотрим на то что настроили

 ![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20ip%20dhcp%20p%20R1.png)

  ![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20ip%20dhcp%20b%20R1.png)

 ![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20ip%20dhcp%20server%20stat.png) 

Попытаемся получить ip по dhcp на PC1

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/PCA%20dhcp.png)


## 3: Сконфигурировать и проверить DHCP Relay на R2

R2

```
interface e0/1
	ip helper-address 10.0.0.1
	end
copy run start
```

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/PCB%20dhcp.png)

ip получили, теперь проверим показатели на роутерах

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20ip%20dhcp%20b%20R1%20posle.png)

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20ip%20dhcp%20s%20s%20R1%20posle%20polucheniya%20ip.png)

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab3/sh%20ip%20dhcp%20s%20s%20R2.png)
