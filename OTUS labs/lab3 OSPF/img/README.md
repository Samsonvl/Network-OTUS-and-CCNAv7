# OSPF. Основные концепции 

Топология
![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab3%20OSPF/img/Топология.png)

Цель: Настроить OSPF офисе Москва
Разделить сеть на зоны
Настроить фильтрацию между зонами

1. Маршрутизаторы R14-R15 находятся в зоне 0 - backbone
2. Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по-умолчанию
3. Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию
4. Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101

## Маршрутизаторы R14-R15 находятся в зоне 0 - backbone

Соединил их дополнительным линком, чтобы только они были ASBR роутерами, а все другие только interior.

R14

```
 no ip domain lookup
 hostname R14
 ipv6 unicast-routing
!
 interface Ethernet0/0
  ip address 10.240.102.14 255.255.255.192
  ipv6 address FE80::14 link-local
  ipv6 address 11AA:BACC:2020:A2::14/64
  ipv6 enable
  ipv6 ospf 1 area 10
  no shutdown
!
 interface Ethernet0/1
  ip address 10.240.103.14 255.255.255.192
  ipv6 address FE80::14 link-local
  ipv6 address 11AA:BACC:2020:A3::14/64
  ipv6 enable
  ipv6 ospf 1 area 10
  no shutdown
!
 interface Ethernet0/2
  ip address 109.126.15.238 255.255.255.252
  ipv6 address FE80::14 link-local
  ipv6 address 11AA:BACC:1000:A2::14/64
  ipv6 enable
  ipv6 ospf 1 area 10
  no shutdown
!
 interface Ethernet0/3
  ip address 10.240.101.14 255.255.255.192
  ipv6 address FE80::14 link-local
  ipv6 address 11AA:BACC:2020:A1::14/64
  ipv6 enable
  ipv6 ospf 1 area 101
  no shutdown
!
 interface Ethernet1/1
  ip address 10.240.107.14 255.255.255.192
  ipv6 address FE80::14 link-local
  ipv6 address 11AA:BACC:2020:A7::14/64
  ipv6 enable
  ipv6 ospf 1 area 0
  no shutdown
!
ip route 0.0.0.0 0.0.0.0 109.126.15.237
!
 router ospf 1
  router-id 14.14.14.14
  area 101 stub no-summary
  network 10.240.107.0 0.0.0.63 area 0
  network 10.240.103.0 0.0.0.63 area 10
  network 10.240.102.0 0.0.0.63 area 10
  network 10.240.101.0 0.0.0.63 area 101
  default-information originate
!
 ipv6 router ospf 1
  router-id 14.14.14.14
  area 101 stub no-summary
!
```

R15

```
 no ip domain lookup
 hostname R15
 ipv6 unicast-routing
!
 interface Ethernet0/0
  ip address 10.240.105.15 255.255.255.192
  ipv6 address FE80::15 link-local
  ipv6 address 11AA:BACC:2020:A5::15/64
  ipv6 enable
  ipv6 ospf 1 area 10
  no shutdown
!
 interface Ethernet0/1
  ip address 10.240.104.15 255.255.255.192
  ipv6 address FE80::15 link-local
  ipv6 address 11AA:BACC:2020:A3::15/64
  ipv6 enable
  ipv6 ospf 1 area 10
  no shutdown
!
 interface Ethernet0/2
  ip address 110.135.25.17 255.255.255.252
  ipv6 address FE80::15 link-local
  ipv6 address 11AA:BACC:2000:A2::15/64
  ipv6 enable
  no shutdown
!
 interface Ethernet0/3
  ip address 10.240.106.15 255.255.255.192
  ipv6 address FE80::15 link-local
  ipv6 address 11AA:BACC:2020:A6::15/64
  ipv6 enable
  ipv6 ospf 1 area 102
  no shutdown
!
 interface Ethernet1/1
  ip address 10.240.107.15 255.255.255.192
  ipv6 address FE80::15 link-local
  ipv6 address 11AA:BACC:2020:A7::15/64
  ipv6 enable
  ipv6 ospf 1 area 0
  no shutdown
!
 ip route 0.0.0.0 0.0.0.0 110.135.25.18
!
 ip prefix-list FILTER-INTO-AREA-102 seq 5 deny 10.240.101.0/26
 ip prefix-list FILTER-INTO-AREA-102 seq 10 permit 0.0.0.0/0 le 32
!
 ipv6 prefix-list FILTERv6-INTO-AREA-102 seq 5 deny 11AA:BACC:2020:A1::/64 le 128
 ipv6 prefix-list FILTERv6-INTO-AREA-102 seq 10 permit ::/0 le 128
!
 router ospf 1
  router-id 15.15.15.15
  area 102 filter-list prefix FILTER-INTO-AREA-102 in
  network 10.240.104.0 0.0.0.63 area 10
  network 10.240.105.0 0.0.0.63 area 10
  network 10.240.106.0 0.0.0.63 area 102
  network 10.240.107.0 0.0.0.63 area 0
  default-information originate
!
 ipv6 router ospf 1
  router-id 15.15.15.15
  area 102 filter-list prefix FILTERv6-INTO-AREA-102 in
```

## Маршрутизаторы R12-R13 находятся в зоне 10. Дополнительно к маршрутам должны получать маршрут по-умолчанию

R12

```
!
 no ip domain lookup
 hostname R12
 ipv6 unicast-routing
!
 interface Ethernet0/2
  ip address 10.240.102.12 255.255.255.192
  ipv6 address FE80::12 link-local
  ipv6 address 11AA:BACC:2020:A2::12/64
  ipv6 enable
  ipv6 ospf 1 area 10 
  no shutdown
!
 interface Ethernet0/3
  ip address 10.240.104.12 255.255.255.192
  ipv6 address FE80::12 link-local
  ipv6 address 11AA:BACC:2020:A4::12/64
  ipv6 enable
  ipv6 ospf 1 area 10
  no shutdown
!
 router ospf 1 
  router-id 12.12.12.12
  network 10.240.102.0 0.0.0.63 area 10
  network 10.240.104.0 0.0.0.63 area 10
!
 ipv6 router ospf 1
  router-id 12.12.12.12
!
```

R13

```
 no ip domain lookup
 hostname R13
 ipv6 unicast-routing
!
 interface Ethernet0/2
  ip address 10.240.105.13 255.255.255.192
  ipv6 address FE80::13 link-local
  ipv6 address 11AA:BACC:2020:A5::13/64
  ipv6 enable
  ipv6 ospf 1 area 10
  no shutdown
!
 interface Ethernet0/3
  ip address 10.240.103.13 255.255.255.192
  ipv6 address FE80::13 link-local
  ipv6 address 11AA:BACC:2020:A3::13/64
  ipv6 enable
  ipv6 ospf 1 area 10
  no shutdown
!
 router ospf 1 
  router-id 13.13.13.13
  network 10.240.103.0 0.0.0.63 area 10
  network 10.240.105.0 0.0.0.63 area 10
!
 ipv6 router ospf 1
  router-id 13.13.13.13
!
```

## Маршрутизатор R19 находится в зоне 101 и получает только маршрут по умолчанию

R19

```
 no ip domain lookup
 hostname R19
 ipv6 unicast-routing
!
 interface Ethernet0/0
  ip address 10.240.101.19 255.255.255.192
  ipv6 address FE80::19 link-local
  ipv6 address 11AA:BACC:2020:A1::19/64
  ipv6 enable
  ipv6 ospf 1 area 101
  no shutdown
!
 router ospf 1
  router-id 19.19.19.19
  area 101 stub
  network 10.240.101.0 0.0.0.63 area 101
!
 ipv6 router ospf 1
  router-id 19.19.19.19
  area 101 stub
!
```

## Маршрутизатор R20 находится в зоне 102 и получает все маршруты, кроме маршрутов до сетей зоны 101

R20

```
 no ip domain lookup
 hostname R20
 ipv6 unicast-routing
!
 line con 0
  exec-timeout 0
!
 interface Ethernet0/0
  ip address 10.240.106.20 255.255.255.192
  ipv6 address FE80::20 link-local
  ipv6 address 11AA:BACC:2020:A6::20/64
  ipv6 enable
  ipv6 ospf 1 area 102
  no shutdown
!
 router ospf 1
  router-id 20.20.20.20 
  network 10.240.106.20 0.0.0.63 area 102
!
 ipv6 router ospf 1
  router-id 20.20.20.20 
!
```

Теперь посмотрим на **sh ip/ipv6 route ospf**

R12
![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab3%20OSPF/img/R12.png)

R13

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab3%20OSPF/img/R13.png)

R14

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab3%20OSPF/img/R14.png)

R15

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab3%20OSPF/img/R15.png)

R19

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab3%20OSPF/img/R19.png)

 R19 получает только маршруты по умолчанию

R20

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab3%20OSPF/img/R20.png)

Нету сетей 10.240.101.0 и 11aa:bacc:2020:a1::/64 которые относятся к зоне 101


