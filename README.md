# Практика RIP
## Карамов Артур КН-202 n=12
## План работы
### 1.
Расположим все необходимые компоненты. Соединим нужными кабелями связи элементы сети.
### 2.
На PC0, PC1, Server1, Server2, Server3 укажем необходимые ip-адресса, маски и шлюз по умолчанию<br>
__PC0__<br>
```ip-address      : 10.12.1.23```<br>
```subnet mask     : 255.255.255.0```<br>
```default gateway : 10.12.1.1```<br>

__PC1__<br>
```ip-address      : 10.12.1.22```<br>
```subnet mask     : 255.255.255.0```<br>
```default gateway : 10.12.1.1```<br>

__Server1__<br>
```ip-address      : 192.168.1.22```<br>
```subnet mask     : 255.255.255.0```<br>

__Server2__<br>
```ip-address      : 192.168.2.22```<br>
```subnet mask     : 255.255.255.0```<br>

__Server3__<br>
```ip-address      : 192.168.3.22```<br>
```subnet mask     : 255.255.255.0```<br>
### 3.

Настроим маршрутизаторы. Привяжем интерфейсы маршрутизатора к конкретным сетям, для этого воспользуемся следующим набором команд.<br>
```
Router>enable<br>
Router#conf t<br>
Router(config)#int fan/k /*где n/k - идентификатор необходимого интерфейса.<br>*/
Router(config-if)#ip address 192.168.2.22 255.255.255.0 /* где вместо 192.168.2.22 255.255.255.0 необходимо поставить данные указаные в тз.<br>
Router(config-if)#no shutdown <br>
Router(config)#exit<br>
Router(config)#wr mem
```
Понятно что эту процедуру необходимо выполнить на всех маршрутизаторах для каждого интерфейса.<br>

### 4.

Настроим протокол RIP на каждом из маршрутизаторов. Приведём пример на Router1
``` Router>enable 
Router#conf t<br>
Router(config)#router rip <br>
Router(config-router)#network 192.168.1.0<br>
Router(config-router)#network 192.168.10.0<br>
Router(config-router)#network 10.12.1.0<br>
Router(config-router)#exit<br>
Router(config)#exit<br>
Router#wr mem<br>
```

### 5. Посмотрим на результаты<br>
__Router1__
``` Router#enable 
Router#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol 
FastEthernet0/0        192.168.10.1    YES manual up                    up 
FastEthernet0/1        10.12.1.1       YES manual up                    up 
FastEthernet1/0        192.168.1.1     YES manual up                    up 
FastEthernet1/1        unassigned      YES unset  administratively down down 
Vlan1                  unassigned      YES unset  administratively down down
show ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/24 is subnetted, 1 subnets
C       10.12.1.0 is directly connected, FastEthernet0/1
C    192.168.1.0/24 is directly connected, FastEthernet1/0
R    192.168.2.0/24 [120/1] via 192.168.10.2, 00:00:11, FastEthernet0/0
R    192.168.3.0/24 [120/1] via 10.12.1.2, 00:00:21, FastEthernet0/1
     192.168.10.0/30 is subnetted, 2 subnets
C       192.168.10.0 is directly connected, FastEthernet0/0
R       192.168.10.4 [120/1] via 192.168.10.2, 00:00:11, FastEthernet0/0
```
__Router2__
```
Router#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol 
FastEthernet0/0        192.168.10.2    YES manual up                    up 
FastEthernet0/1        192.168.10.6    YES manual up                    up 
FastEthernet1/0        192.168.2.1     YES manual up                    up 
FastEthernet1/1        unassigned      YES unset  up                    down 
Vlan1                  unassigned      YES unset  administratively down down

Router#show ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

R    10.0.0.0/8 [120/1] via 192.168.10.1, 00:00:28, FastEthernet0/0
                [120/1] via 192.168.10.5, 00:00:12, FastEthernet0/1
R    192.168.1.0/24 [120/1] via 192.168.10.1, 00:00:28, FastEthernet0/0
C    192.168.2.0/24 is directly connected, FastEthernet1/0
R    192.168.3.0/24 [120/1] via 192.168.10.5, 00:00:12, FastEthernet0/1
     192.168.10.0/30 is subnetted, 2 subnets
C       192.168.10.0 is directly connected, FastEthernet0/0
C       192.168.10.4 is directly connected, FastEthernet0/1
```
__Router3__
```
Router#show ip interface brief 
Interface              IP-Address      OK? Method Status                Protocol 
FastEthernet0/0        10.12.1.2       YES manual up                    up 
FastEthernet0/1        192.168.10.5    YES manual up                    up 
FastEthernet1/0        192.168.3.1     YES manual up                    up 
FastEthernet1/1        unassigned      YES unset  up                    down 
Vlan1                  unassigned      YES unset  administratively down down

Router#show ip route
Codes: C - connected, S - static, I - IGRP, R - RIP, M - mobile, B - BGP
       D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
       N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
       E1 - OSPF external type 1, E2 - OSPF external type 2, E - EGP
       i - IS-IS, L1 - IS-IS level-1, L2 - IS-IS level-2, ia - IS-IS inter area
       * - candidate default, U - per-user static route, o - ODR
       P - periodic downloaded static route

Gateway of last resort is not set

     10.0.0.0/24 is subnetted, 1 subnets
C       10.12.1.0 is directly connected, FastEthernet0/0
R    192.168.1.0/24 [120/1] via 10.12.1.1, 00:00:01, FastEthernet0/0
R    192.168.2.0/24 [120/1] via 192.168.10.6, 00:00:10, FastEthernet0/1
C    192.168.3.0/24 is directly connected, FastEthernet1/0
     192.168.10.0/30 is subnetted, 2 subnets
R       192.168.10.0 [120/1] via 192.168.10.6, 00:00:10, FastEthernet0/1
C       192.168.10.4 is directly connected, FastEthernet0/1

```
