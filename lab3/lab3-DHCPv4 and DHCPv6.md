# lab 3
## DHCPv4
![topology](image.png)

### Part 1
#### Распределение адресного пространства
#### Подсеть А -- 192.168.1.0-63 255.255.255.192
#### Подсеть В -- 192.168.1.64-95 255.255.255.224
#### Подсеть С -- 192.168.1.96-111 255.255.255.240

#### Базовая настройка маршрутизторов 
```
hostname R{1-2}
no domain ip lookup
enable secret class
line console 0
password cisco
login
line vty 0 4
transport input ssh
password cisco
login
service password-encryption
banner motd ^CUnauthorized access is prohibited^C
copy running-config startup-config
clock set MSK 12 12
```
#### Настройка подинтерфейсов маршрутизатора R1

```
interface Ethernet0/1.100
 description Client-network-R1
 encapsulation dot1Q 100
 ip address 192.168.1.1 255.255.255.192
 !
interface Ethernet0/1.200
 description Managment
 encapsulation dot1Q 200
 ip address 192.168.1.65 255.255.255.224
!
interface Ethernet0/1.1000
 encapsulation dot1Q 1000 native
!
interface Ethernet0/2
 ip address 10.0.0.1 255.255.255.252
```
#### Настройка интерфейсов маршрутизатора R2

```
interface Ethernet0/1
 description network C
 ip address 192.168.1.97 255.255.255.240
 ip helper-address 192.168.1.1
!
interface Ethernet0/2
 ip address 10.0.0.2 255.255.255.252
!
ip route 0.0.0.0 0.0.0.0 10.0.0.1
```
#### Базовая настройка коммутатора
```
hostname S{1-2}
no domain ip lookup
enable secret class
line console 0
password cisco
login
line vty 0 4
transport input ssh
password cisco
login
service password-encryption
banner motd ^CUnauthorized access is prohibited^C
copy running-config startup-config
clock set MSK 12 12
```
#### Настройка VLAN на коммутаторах

```
hostname S1
interface Ethernet0/0
 switchport access vlan 100
 switchport mode access
!         
interface Ethernet0/1
 switchport trunk allowed vlan 100,200,1000
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 1000
 switchport mode trunk
!         
interface Ethernet0/2
 shutdown
!         
interface Ethernet0/3
 shutdown
!         
interface Vlan200
 ip address 192.168.1.66 255.255.255.224
!         
ip default-gateway 192.168.1.65
```
```
hostname S2
interface Ethernet0/0
!         
interface Ethernet0/1
 switchport trunk encapsulation dot1q
 switchport mode trunk
!         
interface Ethernet0/2
 shutdown
!         
interface Ethernet0/3
 shutdown
!         
interface Vlan1
 ip address 192.168.1.98 255.255.255.240
!         
ip default-gateway 192.168.1.97
```

### Part 2

#### Настройка пула адресов и relay на маршрутизаторах
##### R1
```
ip dhcp excluded-address 192.168.1.1 192.168.1.5
ip dhcp excluded-address 192.168.1.97
!
ip dhcp pool NET-A
 network 192.168.1.0 255.255.255.192
 default-router 192.168.1.1 
 domain-name ccna-lab.com
 lease 2 12 30
!
ip dhcp pool R2-Client-LAN
 network 192.168.1.96 255.255.255.240
 domain-name ccna-lab.com
 default-router 192.168.1.97 
 lease 2 12 30
 ```

 #### R2
 ``` ip helper-address 192.168.1.1 ```

 ![Просмотр пулов dhcp R1](image-1.png)
 ![Просмотр dhcp R2](image-2.png)

#### Проверка работы DHCP на PC-A и PC-B
![Проверка работы на ПК-В](image-3.png)
![Проверка работы на ПК-А](image-4.png)


## DHCPv6
### Топологию и базовые настройки оставляем прежними
#### Назначаем IP адреса на R1 и R2
```
interface GigabitEthernet0/0
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:2::1/64
 no shutdown
!
interface GigabitEthernet0/1
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
 no shutdown
```
#### Добавление маршрутизации
##### R1
```
ipv6 unicast-routing
ipv6 route 2001:db8:acad:3::/64 2001:db8:acad:2::2
```
##### R2
``` 
ipv6 unicast-routing
ipv6 route 2001:db8:acad:1::/64 2001:db8:acad:2::1 
```

#### Убеждаемся в наличии IP связанности
![IP связанность](image-5.png)

#### Настройка stateless DHCPv6
```
ipv6 dhcp pool R1-STATELESS
 dns-server 2001:DB8:ACAD::254
 domain-name STATELESS.com
```
#### Настрока на интерфейсе
```
interface GigabitEthernet0/1
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
 ipv6 nd other-config-flag
 ipv6 dhcp server R1-STATELESS
```
#### Проверка работы на PC-A
![Проверка на PC-A](image-6.png)

#### Настройка второго пула Statefull DHCPv6 на R1 и relay на R2
```
ipv6 dhcp pool R2-STATEFULL
 address prefix 2001:db8:acad:3:aaa::/80 lifetime 172800 86400
 dns-server 2001:DB:8:ACAD::254
 domain-name STATEFULL.com
```
#### Настрока на интерфейсе R1
```
interface GigabitEthernet0/0
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:1::1/64
 ipv6 dhcp server R2-STATEFULL
```
#### Настрока на интерфейсе R2

```
interface GigabitEthernet0/1
 ipv6 address FE80::1 link-local
 ipv6 address 2001:DB8:ACAD:3::1/64
 ipv6 nd managed-config-flag
 ipv6 enable
 ipv6 dhcp relay destination 2001:db8:acad:2::1 g0/0/0
!
```
#### Проверка на PC-B
![Проверка на PC-B](image-7.png)
