# Лабораторная работа настройка маршрутизация между VLAN с использованием "Router-on-a-Stick"

Топология 


![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab1/Lab%20-%20Configure%20Router-on-a-Stick%20Inter-VLAN%20Routing%20-%20Topology.png)

#### Таблица адресации

| Device | Interface | IP Address   | Subnet Mask   | Default Gateway |
| ------ | --------- | ------------ | ------------- | --------------- |
| R3     | F0/1.3    | 192.168.3.1  | 255.255.255.0 | N/A             |
| R3     | F0/1.4    | 192.168.4.1  | 255.255.255.0 | N/A             |
| R3     | F0/1.8    | N/A          | N/A           | N/A             |
| S3     | VLAN 3    | 192.168.3.11 | 255.255.255.0 | 192.168.3.1     |
| S1     | VLAN 3    | 192.168.3.12 | 255.255.255.0 | 192.168.3.1     |
| PC-S3  | NIC       | 192.168.3.3  | 255.255.255.0 | 192.168.3.1     |
| PC-S1  | NIC       | 192.168.4.3  | 255.255.255.0 | 192.168.4.1     |

#### VLAN Таблица

| VLAN | Name       | Interface Assigned            |
| ---- | ---------- | ----------------------------- |
| 3    | Management | S1: VLAN 3                    |
|      |            | S3: VLAN 3                    |
|      |            | S3: F0/15                     |
| 4    | Operations | S1: F0/15                     |
| 7    | ParkingLot | S1: F0/2-4, F0/7-24, G0/1-2   |
|      |            | S2: F0/2-17, F0/19-24, G0/1-2 |
| 8    | Native     | N/A                           |

# Содержание

1: Построить сеть и произвести базовую настройку

2: Создать VLAN и добавить их на порты согласно таблице

3: Настроить 802.1Q магистраль между коммутаторами

4: Настроить Inter-VLAN Routing на маршрутизаторе

5: Проверить Inter-VLAN Routing на работоспособность

## 1: Построить сеть и произвести базовую настройку

Пример базовой настройки 

```
no ip domain-lookup
hostname R3
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

## 2: Создать VLAN и добавить их на интерфейсы согласно таблице

Создадим вланы

S1, S3

```
vlan 3
	name Management
vlan 4
	name Operations
vlan 7
	name ParkingLot
vlan 8 
	name Native
```

Настроим SVI и шлюз по умолчанию. Добавил ip адреса согласно таблице:
S1

```
int vlan 3 
	ip add 192.168.3.11 255.255.255.0 
	exit
ip default-gateway 192.168.3.1
```

S3

```
int vlan 3 
	ip add 192.168.3.12 255.255.255.0 
	exit
ip default-gateway 192.168.3.1
```

Добавим не использованные порты в влан ParkingLot, настроим access mode и выключим их
S1

```
inteface range fa 0/2-14, fa 0/16-24
	switchport mode access
	switchport access vlan 7
	shutdown
interface range gigabitEthernet 0/1 - 2
	switchport mode access
	switchport access vlan 7
	shutdown	
```

S3

```
interface range fastEthernet 0/1 - 2, fastEthernet 0/4
	switchport mode access
	switchport access vlan 7
	shutdown 
interface range fastEthernet 0/6 - 14, fastEthernet 0/16 - 24
	switchport mode access
	switchport access vlan 7
	shutdown
inteface range G0/1 - 2
	switchport mode access
	switchport access vlan 7
	shutdown
```

Добавим вланы на используемые интерфейсы
S1

```
interface f0/15
	switchport mode access
	switchport access vlan 4
```

S3

```
interface f0/15
	switchport mode access
	switchport access vlan 3
```

Воспользуемся командой **show vlan** **brief**  чтобы проверить вланы

S1

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab1/S1%20sh%20vlan%20brief.png)

S3
![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab1/S3%20sh%20vlan%20brief.png)

## 3: Настроить 802.1Q магистраль между коммутаторами

Настроим транк между коммутаторами, добавим нативный влан и разрешим только трафик с абонентских вланов

S1

```
interface f0/1
	switchport mode trunk
	switchport trunk native vlan 8
	switchport trunk allowed vlan remove 1
	switchport trunk allowed vlan remove 7
```

S3

```
interface f0/3
	switchport mode trunk
	switchport trunk native vlan 8
	switchport trunk allowed vlan remove 1
	switchport trunk allowed vlan remove 7
```

Настроим интерфейс в сторону роутера как транк

S3

```
interface f0/5
	switchport mode trunk
```

Проверим командой **show interfaces trunk** 

S1

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab1/S1%20sh%20interface%20trunk.png)

S3

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab1/S3%20sh%20interface%20trunk.png)

## 4: Настроить Inter-VLAN Routing на маршрутизаторе

Включим порт на роутера и настроим саб интерфейсы согласно таблице 
R1

```
interface f0/1.3
	description Management_Network
	encapsulation dot1q 3
	ip address 192.168.3.1 255.255.255.0
interface f0/1.4
	description Operations_Network
	encapsulation dot1q 4
	ip address 192.168.4.1 255.255.255.0
interface f0/1.8
	description Native_VLAN
	encapsulation dot1q 8 native
```

Воспользуемся командой **show ip interface brief**  для проверки 
R1

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab1/R3%20sh%20ip%20interface%20brief.png)

## 5: Проверить Inter-VLAN Routing на работоспособность

Выполним пинги с PS-S3 на:
шлюз по умолчанию
С PS-S3 на PS-S1
На S2

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab1/все%20пинги.png)
Потерянный пакет это сработал протокол ARP

И наоборот все те-же пинги только с PS-S1 и добавим трассировку до PS-S3

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab1/все%20пинги%20и%20трасерт.png)


