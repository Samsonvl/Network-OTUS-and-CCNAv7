# Протокол IP

Топология 

![](https://github.com/Samsonvl/network-otus/blob/master/labs/lab10/screenshots/Топология%20в%20draw.io%20.jpg)


Цель: В данной самостоятельной работе необходимо распланировать адресное пространство Настроить IP на всех активных портах для дальнейшей работы над проектом Адресное пространство должно быть задокументировано

Задача

1. разработать и задокументировать адресное пространство для лабораторного стенда.
2. настроить ip адреса на каждом активном порту
3. настроить каждый VPC в любом офисе в своем VLAN.
4. настроить VLAN управления для сетевых устройств
5. настроить сети офисов так, чтобы не возникало broadcast штормов, а использование линков было максимально оптимизировано 
6. использовать ipv4 и ipv6

# Разработать и задокументировать адресное пространство для лабораторного стенда.

Общая таблица сетей

| Connected | Network IPv4      | Network IPv6           |
| --------- | ----------------- | ---------------------- |
| R19-R14   | 10.240.101.0/26   | 11aa:bacc:2020:a1::/64 |
| R12-R14   | 10.240.102.0/26   | 11aa:bacc:2020:a2::/64 |
| R14-R13   | 10.240.103.0/26   | 11aa:bacc:2020:a3::/64 |
| R12-R15   | 10.240.104.0/26   | 11aa:bacc:2020:a4::/64 |
| R15-R13   | 10.240.105.0/26   | 11aa:bacc:2020:a5::/64 |
| R15-R20   | 10.240.106.0/26   | 11aa:bacc:2020:a6::/64 |
| R14-R22   | 109.126.15.236/30 | 11aa:bacc:1000:a2::/64 |
| R15-R21   | 110.135.25.16/30  | 11aa:bacc:2000:a2::/64 |
| R22-R21   | 109.126.16.236/30 | 11aa:bacc:1000:a1::/64 |
| R22-R23   | 109.126.17.236/30 | 11aa:bacc:1000:a3::/64 |
| R21-R24   | 111.147.15.168/30 | 11aa:bacc:3000:b::/64  |
| R23-R25   | 30.40.1.0/26      | 11aa:bacc:2020:c3::/64 |
| R23-R24   | 30.40.2.0/26      | 11aa:bacc:2020:c4::/64 |
| R24-R26   | 30.40.3.0/26      | 11aa:bacc:2020:c1::/64 |
| R26-R25   | 30.40.4.0/26      | 11aa:bacc:2020:c2::/64 |
| R25-R27   | 109.126.18.236/30 | 11aa:bacc:1000:a1::/64 |
| R25-R28   | 109.126.19.236/30 | 11aa:bacc:1000:a2::/64 |
| R26-R28   | 109.126.20.236/30 | 11aa:bacc:1000:c1::/64 |
| R18-R24   | 111.147.14.168/30 | 11aa:bacc:3000:b2::/64 |
| R18-R24   | 111.147.15.180/30 | 11aa:bacc:3000:b3::/64 |
| R18-R16   | 20.140.201.0/28   | 11aa:bacc:2020:b1::/64 |
| R18-R17   | 20.140.205.0/28   | 11aa:bacc:2020:b3::/64 |
| R16-R32   | 20.140.203.0/28   | 11aa:bacc:2020:b3::/64 |

## Настроить ip адреса на каждом активном порту

Адреса IPv4 будут, где это возможно, в последнем октете иметь цифру, совпадающую с номером маршрутизатора на схеме, например, для маршрутизатора **R12** адрес будет иметь вид **10.240.100.12**

Адреса IPv6 будут, где это возможно, в последнем октете иметь цифру, совпадающую с номером маршрутизатора на схеме, например, для маршрутизатора **R12** адрес будет иметь вид **11AA:BACC:2020:A::12/64**.

Link-local адреса IPv6 будут в последнем октете иметь цифру, совпадающую с номером маршрутизатора на схеме, например, для маршрутизатора **R12** все link-local адреса будут иметь вид **FE80::\**12\****. Link-local адреса IPv6 располагаются в подсети `FE80::/10`.Таблица адресации

| Устройство | Интерфейс | IPv4 адрес     | Маска подсети   | IPv6 адрес               | Описание           | Link-Local |
| ---------- | --------- | -------------- | --------------- | ------------------------ | ------------------ | ---------- |
| R12        | e0/2      | 10.240.102.12  | 255.255.255.192 | 11AA:BACC:2020:A2::12/64 | R12->R14           | FE80::12   |
|            | e0/3      | 10.240.104.12  | 255.255.255.192 | 11AA:BACC:2020:A4::12/64 | R12->R15           | FE80::12   |
| R13        | e0/2      | 10.240.105.13  | 255.255.255.192 | 11AA:BACC:2020:A5::13/64 | R13->R15           | FE80::13   |
|            | e0/3      | 10.240.103.13  | 255.255.255.192 | 11AA:BACC:2020:A3::13/64 | R13->R14           | FE80::13   |
| R14        | e0/0      | 10.240.102.14  | 255.255.255.192 | 11AA:BACC:2020:A2::14/64 | R14->R12           | FE80::14   |
|            | e0/1      | 10.240.103.14  | 255.255.255.192 | 11AA:BACC:2020:A3::14/64 | R14->R13           | FE80::14   |
|            | e0/2      | 109.126.15.238 | 255.255.255.252 | 11AA:BACC:1000:A2::14/64 | R14->R22(ISP)      | FE80::14   |
|            | e0/3      | 10.240.101.14  | 255.255.255.192 | 11AA:BACC:2020:A1::14/64 | R14->R19           | FE80::14   |
| R15        | e0/0      | 10.240.105.15  | 255.255.255.192 | 11AA:BACC:2020:A5::15/64 | R15->R13           | FE80::15   |
|            | e0/1      | 10.240.104.15  | 255.255.255.192 | 11AA:BACC:2020:A4::15/64 | R15->R12           | FE80::15   |
|            | e0/2      | 110.135.25.17  | 255.255.255.252 | 11AA:BACC:2000:A2::15/64 | R15->R21(ISP)      | FE80::15   |
|            | e0/3      | 10.240.106.15  | 255.255.255.192 | 11AA:BACC:2020:A6::15/64 | R15->R20           | FE80::15   |
| R16        | e0/1      | 20.140.201.2   | 255.255.255.240 | 11AA:BACC:2020:B1::16/64 | R16->R18           | FE80::16   |
|            | e0/3      | 20.140.203.4   | 255.255.255.240 | 11AA:BACC:2020:B3::16/64 | R16->R32           | FE80::16   |
| R17        | e0/1      | 20.140.205.6   | 255.255.255.240 | 11AA:BACC:2020:B3::17/64 | R17->R18           | FE80::17   |
| R18        | e0/0      | 20.140.201.3   | 255.255.255.240 | 11AA:BACC:2020:B1::18/64 | R18->R16           | FE80::18   |
|            | e0/1      | 20.140.205.7   | 255.255.255.240 | 11AA:BACC:2020:B3::18/64 | R18->R17           | FE80::18   |
|            | e0/2      | 111.147.14.170 | 255.255.255.252 | 11AA:BACC:3000:B2::18/64 | R18->R24(ISP)      | FE80::18   |
|            | e0/3      | 111.147.16.181 | 255.255.255.252 | 11AA:BACC:3000:B3::18/64 | R18->R26(ISP)      | FE80::18   |
| R19        | e0/0      | 10.240.101.40  | 255.255.255.192 | 11AA:BACC:2020:A1::19/64 | R19->R14           | FE80::19   |
| R20        | e0/0      | 10.240.103.44  | 255.255.255.192 | 11AA:BACC:2020:A3::20/64 | R20->R15           | FE80::20   |
| R21        | e0/0      | 110.135.25.18  | 255.255.255.252 | 11AA:BACC:2000:A2::21/64 | R21(ISP)->R15      | FE80::21   |
|            | e0/1      | 109.126.16.238 | 255.255.255.252 | 11AA:BACC:1000:A1::21/64 | R21(ISP)->R22(ISP) | FE80::21   |
|            | e0/2      | 111.147.15.170 | 255.255.255.252 | 11AA:BACC:3000:B::21/64  | R21(ISP)->R24(ISP) | FE80::21   |
| R22        | e0/0      | 109.126.15.237 | 255.255.255.252 | 11AA:BACC:1000:A2::22/64 | R22(ISP)->R14      | FE80::22   |
|            | e0/1      | 109.126.16.237 | 255.255.255.252 | 11AA:BACC:1000:A1::22/64 | R22(ISP)->R21(ISP) | FE80::22   |
|            | e0/2      | 109.126.17.237 | 255.255.255.252 | 11AA:BACC:1000:A3::22/64 | R22(ISP)->R23(ISP) | FE80::22   |
| R23        | e0/0      | 109.126.17.238 | 255.255.255.252 | 11AA:BACC:1000:A3::23/64 | R23(ISP)->R22(ISP) | FE80::23   |
|            | e0/1      | 30.40.1.10     | 255.255.255.192 | 11AA:BACC:2020:C1::23/64 | R23->R25           | FE80::23   |
|            | e0/2      | 30.40.2.20     | 255.255.255.192 | 11AA:BACC:2020:C2::23/64 | R23->R24           | FE80::23   |
| R24        | e0/0      | 111.147.15.169 | 255.255.255.252 | 11AA:BACC:3000:B::18/64  | R24(ISP)->R21(ISP) | FE80::24   |
|            | e0/1      | 30.40.3.1      | 255.255.255.192 | 11AA:BACC:2020:C3::24/64 | R24->R26           | FE80::24   |
|            | e0/2      | 30.40.2.23     | 255.255.255.192 | 11AA:BACC:2020:C2::24/64 | R24->R23           | FE80::24   |
|            | e0/3      | 111.147.14.169 | 255.255.255.252 | 11AA:BACC:3000:B2::24/64 | R24(ISP)->R18      | FE80::24   |
| R25        | e0/0      | 30.40.1.11     | 255.255.255.192 | 11AA:BACC:2020:C1::25/64 | R25->R23           | FE80::25   |
|            | e0/1      | 109.126.18.238 | 255.255.255.252 | 11AA:BACC:1000:A1::25/64 | R25->R27           | FE80::25   |
|            | e0/2      | 30.40.4.12     | 255.255.255.192 | 11AA:BACC:2020:C4::25/64 | R25->R26           | FE80::25   |
|            | e0/3      | 109.126.19.238 | 255.255.255.252 | 11AA:BACC:1000:A2::25/64 | R25->R28           | FE80::25   |
| R26        | e0/0      | 30.40.3.2      | 255.255.255.192 | 11AA:BACC:2020:C3::26/64 | R26->R24           | FE80::26   |
|            | e0/1      | 109.126.20.238 | 255.255.255.252 | 11AA:BACC:1000:С1::26/64 | R26->R28           | FE80::26   |
|            | e0/2      | 30.40.4.13     | 255.255.255.192 | 11AA:BACC:2020:C4::26/64 | R26->R25           | FE80::26   |
|            | e0/3      | 111.147.16.182 | 255.255.255.252 | 11AA:BACC:3000:B3::26/64 | R26->R18           | FE80::26   |
| R27        | e0/0      | 109.126.18.237 | 255.255.255.252 | 11AA:BACC:1000:A1::27/64 | R27->R25           | FE80::27   |
| R28        | e0/0      | 109.126.20.237 | 255.255.255.252 | 11AA:BACC:1000:С1::28/64 | R28->R26           | FE80::28   |
|            | e0/1      | 109.126.19.237 | 255.255.255.252 | 11AA:BACC:1000:A2::28/64 | R28->R25           | FE80::28   |
| R32        | e0/0      | 20.140.203.5   | 255.255.255.240 | 11AA:BACC:2020:B3::32/64 | R32->R16           | FE80::32   |

##### Пример настройки на маршрутизаторе R12:

```
conf t
 ipv6 unicast-routing
!
 interface Ethernet0/2
  ip address 10.240.102.12 255.255.255.192
  ipv6 address FE80::12 link-local
  ipv6 address 11AA:BACC:2020:A2::12/64
  ipv6 enable
  no shutdown
  exit
!
 interface Ethernet0/3
  ip address 10.240.104.12 255.255.255.192
  ipv6 address FE80::12 link-local
  ipv6 address 11AA:BACC:2020:A4::12/64
  ipv6 enable
  no shutdown
  exit
```



### Настроить каждый VPC в любом офисе в своем VLAN.

Таблица настроек VPC

| Устройство | Интерфейс | IPv4 адрес   | Маска подсети | Шлюз         | VLAN   |
| ---------- | --------- | ------------ | ------------- | ------------ | ------ |
| VPC1       | NIC       | 192.168.10.6 | 255.255.255.0 | 192.168.10.1 | VLAN10 |
| VPC7       | NIC       | 192.168.20.6 | 255.255.255.0 | 192.168.20.1 | VLAN20 |
| VPC8       | NIC       | 192.168.30.2 | 255.255.255.0 | 192.168.30.1 | VLAN30 |
| VPC11      | NIC       | 192.168.40.2 | 255.255.255.0 | 192.168.40.1 | VLAN40 |
| VPC30      | NIC       | 192.168.50.3 | 255.255.255.0 | 192.168.50.1 | VLAN50 |
| VPC31      | NIC       | 192.168.60.3 | 255.255.255.0 | 192.168.60.1 | VLAN60 |

## Настройка VLAN управления для сетевых устройств и настройка сети офисов так, чтобы не возникало broadcast штормов, а использование линков было максимально оптимизировано

R28

```
R28(config)#int e0/2.50
R28(config-subif)#encapsulation dot1q 50
R28(config-subif)# ip address 192.168.50.1 255.255.255.0
R28(config)#int e0/2.60
R28(config-subif)#encapsulation dot1q 60
R28(config-subif)# ip address 192.168.60.1 255.255.255.0
```

SW29

```
SW29# configure terminal
SW29(config)# vlan 50
SW29(config-vlan)# name 50
SW29(config-vlan)# exit
SW29(config)# vlan 60
SW29(config-vlan)# name 60
SW29(config-vlan)# exit
SW29(config)# interface vlan 50
SW29(config-if)# ip address 192.168.10.2 255.255.255.0
SW29(config-if)# exit
SW29(config)# interface vlan 60
SW29(config-if)# ip address 192.168.20.2 255.255.255.0
SW29(config-if)# exit
SW29(config)#int e0/1
SW29(config-if)#switchport mode access
SW29(config-if)#switchport access vlan 60
SW29(config-if)#exit
SW29(config)#int e0/0
SW29(config-if)#switchport mode access
SW29(config-if)#switchport access vlan 50
SW29(config-if)#exit
SW29(config)#int e0/2
SW29(config-if)#switchport trunk encapsulation dot1q
SW29(config-if)#switchport mode trunk
SW29(config-if)#exit
```

SW9

```

vlan 30
	name 30
	exit
vlan 40
	name 40
	exit
vlan 88
	name 88
	exit
int range e0/0-1
	channel-group 1 mode active
	exit
int port-channel 1 
	switchport trunk encapsulation dot1q
	switchport mode trunk
	exit
port-channel load-balance dst-ip
vlan 99
	name Management
int po1
	switchport trunk native vlan 99
	switchport trunk allowed vlan add 30,88,40
	exit
int e0/2
	switchport mode access
	switchport access vlan 30
int e0/1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk allowed vlan add 30,88,40
	exit
int vlan 30
	ip address 192.168.30.6 255.255.255.0
	no shut
int vlan 40
	ip address 192.168.40.6 255.255.255.0
	no shut
int vlan 88
	ip address 10.88.88.9 255.255.255.0
	no shut
```

SW10

```
vlan 30
	name 30
	exit
vlan 40
	name 40
	exit
vlan 88
	name 88
	exit
int range e0/0-1
	channel-group 1 mode active
	exit
int port-channel 1 
	switchport encapsulation dot1q
	switchport mode trunk
	exit
port-channel load-balance dst-ip
vlan 99
	name Management
int po1
	switchport trunk native vlan 99
	switchport trunk allowed vlan add 40,30,88
	exit
int e0/2
	switchport mode access
	switchport access vlan 40
int e0/1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk allowed vlan add 30,88,40
	exit
int vlan 30
	ip address 192.168.30.5 255.255.255.0
	no shut
int vlan 40
	ip address 192.168.40.5 255.255.255.0
	no shut
int vlan 88
	ip address 10.88.88.10 255.255.255.0
	no shut
```

R16

```
int e0/2.30
	encapsulation dot1q 30
	ip address 192.168.30.1 255.255.255.0
int e0/2.40
	encapsulation dot1q 40
	ip address 192.168.40.1 255.255.255.0
int e0/2.88
	encapsulation dot1q 88
	ip address 10.88.88.1 255.255.255.0
```

R17

```
int e0/2.30
	encapsulation dot1q 30
	ip address 192.168.30.1 255.255.255.0
int e0/2.40
	encapsulation dot1q 40
	ip address 192.168.40.1 255.255.255.0
int e0/2.88
	encapsulation dot1q 88
	ip address 10.88.88.1 255.255.255.0
```

SW2

```
vlan 20
	name 20
	exit
vlan 77
	name 77
	exit
int e0/2
	switchport mode access
	switchport access vlan 20
	exit
int e0/1
	switchport trunk native vlan 99
	switchport trunk encapsulation dot1q
	switchport mode trunk
int e0/0
	switchport trunk native vlan 99
	switchport trunk encapsulation dot1q
	switchport mode trunk
	exit
int vlan 77
	ip address 10.77.77.2 255.255.255.0
```

SW3

```
vlan 10
	name 10
vlan 77
	name 77
	exit
int e0/2
	switchport mode access
	switchport access vlan 10
	exit
int e0/1
	switchport trunk native vlan 99
	switchport trunk encapsulation dot1q
	switchport mode trunk
int e0/0
	switchport trunk native vlan 99
	switchport trunk encapsulation dot1q
	switchport mode trunk
	exit
int vlan 77
	ip address 10.77.77.3 255.255.255.0 
```

SW4

```
int range e0/2-3
	channel-group 1 mode active
	exit
int port-channel 1 
	switchport trunk encapsulation dot1q
	switchport mode trunk
	exit
port-channel load-balance dst-ip
int po1
	switchport trunk native vlan 99
	switchport trunk allowed vlan add 10,20,77
	exit
int e0/0
	switchport trunk encapsulation dot1q
	switchport mode trunk
int e0/1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk allowed vlan add 10,20,77
	exit
int vlan 10
	ip address 192.168.10.2 255.255.255.0
	no shut
int vlan 20
	ip address 192.168.20.2 255.255.255.0
	no shut
int vlan 77
	ip address 10.77.77.4 255.255.255.0
	no shut
int e1/1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk native vlan 99
	switchport trunk allowed vlan add 10,20,77
```

SW5

```
int range e0/2-3
	channel-group 1 mode active
	exit
int port-channel 1 
	switchport trunk encapsulation dot1q
	switchport mode trunk
	exit
port-channel load-balance dst-ip
int po1
	switchport trunk native vlan 99
	switchport trunk allowed vlan add 10,20,77
	exit
int e0/0
	switchport trunk encapsulation dot1q
	switchport mode trunk
int e0/1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk allowed vlan add 10,20,77
	exit
int vlan 10
	ip address 192.168.10.3 255.255.255.0
	no shut
int vlan 20
	ip address 192.168.20.3 255.255.255.0
	no shut
int vlan 77
	ip address 10.77.77.5 255.255.255.0
int e1/1
	switchport trunk encapsulation dot1q
	switchport mode trunk
	switchport trunk native vlan 99
	switchport trunk allowed vlan add 10,20,77
```

R12

```
int e0/1.10
	encapsulation dot1q 10
	ip address 192.168.10.1 255.255.255.0
int e0/1.20
	encapsulation dot1q 20
	ip address 192.168.20.1 255.255.255.0
int e0/1.77
	encapsulation dot1q 77
	ip address 10.77.77.1 255.255.255.0
```

R13

```
int e0/1.10
	encapsulation dot1q 10
	ip address 192.168.10.1 255.255.255.0
int e0/1.20
	encapsulation dot1q 20
	ip address 192.168.20.1 255.255.255.0
int e0/1.77
	encapsulation dot1q 77
	ip address 10.77.77.1 255.255.255.0
```



