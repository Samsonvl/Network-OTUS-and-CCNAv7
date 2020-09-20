# Lab - Configure DHCPv6


Топология

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab4/Топология.png)

#### Таблица адресации

| Device | Interface | IPv6 Address           |
| ------ | --------- | ---------------------- |
| R1     | e0/0      | 2001:db8:acad:2::1/64  |
|        |           | fe80::1                |
|        | e0/1      | 2001:db8:acad:1::1/64  |
|        |           | fe80::1                |
| R2     | e0/0      | 2001:db8:acad:2::2/64  |
|        |           | fe80::2                |
| R2     | e0/1      | 2001:db8:acad:3::1 /64 |
|        |           | fe80::1                |
| PC-S1  | NIC       | DHCP                   |
| PC-S2  | NIC       | DHCP                   |

#### Содержание

1. Создать сеть и произвести базовую настройку устройств
2. Подтвердить назначение адресов от R1 по SLAAC
3. Настроить и проверить DHCPv6 без отслеживания состояния на R1
4. Настроить и проверить DHCPv6 с отслеживанем состояния на R1
5. Настроить и проверить DHCPv6 Relay на R2

## Создать сеть и произвести базовую настройку устройств

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
ipv6 unicast-routing
exit
clock set 14:00:00 09 July 2020
copy runnig-config startap-config

```

Настроим интерфейсы и маршрутизацию

R1

```
interface e0/0
	ipv6 add 2001:db8:acad:2::1/64
	ipv6 add fe80::1 link-local
	no shut
	exit
interface e0/1
	ipv6 add 2001:db8:acad:1::1/64
	ipv6 add fe80::1 link-local
	no shut
	exit
ipv6 route ::/0 2001:db8:acad:2::2
```



R2

```
interface e0/0
	ipv6 add 2001:db8:acad:2::2/64
	ipv6 add fe80::2 link-local
	no shut
	exit
interface e0/1
	ipv6 add 2001:db8:acad:3::1/64
	ipv6 add fe80::1 link-local
	no shut
	exit
ipv6 route ::/0 2001:db8:acad:2::1
```

Пропингуем интерфес e0/1 на R2 c R1

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab4/ping%2031.png)

## 2: Подтвердить назначение адресов от R1 по SLAAC

После включения PCA и команды **sh** должны показать, что компьютер присвоил себе адрес из сети 2001: db8: 1 :: / 64.

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab4/sh.png)

## 3: Настроить и проверить DHCPv6 без отслеживания состояния на R1

```
R1(config)# ipv6 dhcp pool R1-STATELESS
R1(config-dhcp)# dns-server 2001:db8:acad::254
R1(config-dhcp)# domain-name STATELESS.com
R1(config)# interface e0/1
R1(config-if)# ipv6 nd other-config-flag
R1(config-if)# ipv6 dhcp server R1-STATELESS

```

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab4/PCA.png)

Для теста пингонем e0/1 на R2
![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab4/ping%2031.png)

## 4: Настроить и проверить DHCPv6 с отслеживанем состояния на R1

Настроим маршрутизатор R1 для ответа на запросы DHCPv6 из локальной сети на маршрутизаторе R2.

```
R1(config)# ipv6 dhcp pool R2-STATEFUL
R1(config-dhcp)# address prefix 2001:db8:acad:3:aaa::/80
R1(config-dhcp)# dns-server 2001:db8:acad::254
R1(config-dhcp)# domain-name STATEFUL.com
R1(config)# interface e0/0
R1(config-if)# ipv6 dhcp server R2-STATEFUL
```

## 5: Настроить и проверить DHCPv6 Relay на R2

```
R2(config)# interface e0/1
R2(config-if)# ipv6 nd managed-config-flag
R2(config-if)# ipv6 dhcp relay destination 2001:db8:acad:2::1 e0/0

```

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab4/PCB.png)

Проверим пингом от PCB до e0/1 R1

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/CCNAv7%20labs/lab4/ping%2011.png)
