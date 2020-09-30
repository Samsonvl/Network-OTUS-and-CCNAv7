# Основные концепции EIGRP

EIGRP 

Топология

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab4/img/Топология.png)

Цель: Построение сети и проверка соединения
Настройка маршрутизации EIGRP

*EIGRP расширенный
Создание сети и настройка основных параметров устройства
Настройка EIGRP и проверка подключения
Настройка EIGRP для автоматического суммирования
Настройка и распространение статического маршрута по умолчанию



В этой лабораторной работе необходимо настроить EIGRP:

1. EIGRP в части схемы СПБ
2. EIGRP использует суммарные маршруты, для оптимизации протокола
3. Используется распределение нагрузки по всем возможным линкам
4. Аналогичную работу реализовать для IPv6

5*. Использовать расширенный(именованный) EIGRP для IPv4 и IPv6 вместо стандартной настройки

Попробую сделать по именованному методу
R16

```
 router eigrp DUAL-STACK
  address-family ipv4 unicast autonomous-system 4
   eigrp router-id 16.16.16.16
   network 20.140.203.0 0.0.0.63
   network 20.140.201.0 0.0.0.63
  af-interface e0/2
   passive-interface
   exit-af-interface 
  exit-address-family
  address-family ipv6 unicast autonomous-system 6
   eigrp router-id 16.16.16.16 
  af-interface e0/2
   passive-interface
   exit-af-interface 
  exit-address-family
```

R17

```
 router eigrp DUAL-STACK
  address-family ipv4 unicast autonomous-system 4
   eigrp router-id 17.17.17.17
   network 20.140.205.0 0.0.0.63 
  af-interface e0/2
   passive-interface
   exit-af-interface
  exit-address-family
  address-family ipv6 unicast autonomous-system 6
   eigrp router-id 17.17.17.17  
  af-interface e0/2
   passive-interface
   exit-af-interface
  exit-address-family
!
```

R18

```
 router eigrp DUAL-STACK
  address-family ipv4 unicast autonomous-system 4
   eigrp router-id 18.18.18.18
   network 20.140.201.0 0.0.0.63
   network 20.140.205.0 0.0.0.63
  exit-address-family
  address-family ipv6 unicast autonomous-system 6
   eigrp router-id 18.18.18.18
  exit-address-family
!
 ip route 0.0.0.0 0.0.0.0 111.147.16.182
 ipv6 route ::/0 e0/3 11aa:bacc:3000:b3::26
 ip route 0.0.0.0 0.0.0.0 111.147.14.169
 ipv6 route ::/0 e0/2 11aa:bacc:3000:b2::24
!
```

R32

```
 router eigrp DUAL-STACK
  address-family ipv4 unicast autonomous-system 4
   eigrp router-id 32.32.32.32
   network 20.140.203.0 0.0.0.63
  exit-address-family
  address-family ipv6 unicast autonomous-system 6
   eigrp router-id 32.32.32.32
  exit-address-family
!
```

Посмотрим на **sh ip/ipv6 route eigrp**

R16

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab4/img/R16.png)

R17

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab4/img/R17.png)

R18

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab4/img/R18.png)

R32

![](https://github.com/Samsonvl/Network-OTUS-and-CCNAv7/blob/master/OTUS%20labs/lab4/img/R32.png)

Все маршруты eigrp видно. Маршрут по умолчанию тоже видно. Как по мне этот метод проще обычной настройки.
