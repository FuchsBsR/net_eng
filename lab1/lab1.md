# Лабораторная работа № 1
## Топология
![Топология](image-1.png)

### Config Router
```
version 15.4
service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption
!
hostname Router-on-Stick
!
boot-start-marker
boot-end-marker
!
!
enable secret 5 $1$3OvE$khxpQgREq0arG2e28aGPg1
!
no aaa new-model
clock timezone MSK 18 17
mmi polling-interval 60
no mmi auto-configure
no mmi pvc
mmi snmp-timeout 180
!
!         
!         
!         
!         
!         
!         
!         
          
          
!         
!         
!         
!         
no ip domain lookup
ip cef    
no ipv6 cef
!         
multilink bundle-name authenticated
!         
!         
!         
!         
!         
!         
!         
!         
!         
redundancy
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
interface Ethernet0/0
 no ip address
!         
interface Ethernet0/0.10
 encapsulation dot1Q 10
 ip address 192.168.10.1 255.255.255.0
!         
interface Ethernet0/0.20
 encapsulation dot1Q 20
 ip address 192.168.20.1 255.255.255.0
!         
interface Ethernet0/0.30
 encapsulation dot1Q 30
 ip address 192.168.30.1 255.255.255.0
!         
interface Ethernet0/0.1000
 encapsulation dot1Q 1000
!         
interface Ethernet0/1
 no ip address
 shutdown 
!         
interface Ethernet0/2
 no ip address
 shutdown 
!         
interface Ethernet0/3
 no ip address
 shutdown 
!         
ip forward-protocol nd
!         
!         
no ip http server
no ip http secure-server
!         
!         
!         
!         
control-plane
!         
!         
!         
!         
!         
!         
!         
banner motd ^CAuthentification is nessesary^C
!         
line con 0
 password 7 02050D480809
 logging synchronous
 login    
line aux 0
line vty 0 4
 password 7 05080F1C2243
 login    
 transport input ssh
!         
!         
end       
```
### Config Switch 1
```
service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption
service compress-config
!
hostname SW-1
!
boot-start-marker
boot-end-marker
!
!
enable secret 5 $1$.ZEi$gMBLKUbJ5JDecJ0i90t4//
!
no aaa new-model
clock timezone MSK 18 19
!
!
!         
!         
!         
!         
!         
!         
no ip domain-lookup
ip cef    
no ipv6 cef
!         
!         
spanning-tree mode pvst
spanning-tree extend system-id
!         
vlan internal allocation policy ascending
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
interface Ethernet0/0
 switchport access vlan 20
 switchport mode access
!         
interface Ethernet0/1
 switchport trunk allowed vlan 10,20,30,1000
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 1000
 switchport mode trunk
!         
interface Ethernet0/2
 switchport trunk allowed vlan 10,20,30,1000
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 1000
 switchport mode trunk
!         
interface Ethernet0/3
 switchport access vlan 999
 switchport mode access
 shutdown 
!         
interface Vlan10
 ip address 192.168.10.11 255.255.255.0
!         
ip default-gateway 192.168.10.1
ip forward-protocol nd
!         
no ip http server
no ip http secure-server
!         
!         
!         
!         
!         
!         
control-plane
!         
banner motd ^CAuthentification is nessesary^C
!         
line con 0
 password 7 1511021F0725
 logging synchronous
 login    
line aux 0
line vty 0 4
 password 7 0822455D0A16
 login    
 transport input ssh
!         
!         
end  
```

### Config Switch 2

```
version 15.2
service timestamps debug datetime msec
service timestamps log datetime msec
service password-encryption
service compress-config
!
hostname SW-2
!
boot-start-marker
boot-end-marker
!
!
enable secret 5 $1$jH.V$wZ7cYRbGK2l3aYcjbYMJT1
!
no aaa new-model
clock timezone MSK 18 24
!
!
!         
!         
!         
!         
!         
!         
no ip domain-lookup
ip cef    
no ipv6 cef
!         
!         
spanning-tree mode pvst
spanning-tree extend system-id
!         
vlan internal allocation policy ascending
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
!         
interface Ethernet0/0
 switchport access vlan 30
 switchport mode access
!         
interface Ethernet0/1
 switchport trunk allowed vlan 10,20,30,1000
 switchport trunk encapsulation dot1q
 switchport trunk native vlan 1000
 switchport mode trunk
!         
interface Ethernet0/2
 switchport access vlan 999
 switchport mode access
 shutdown 
!         
interface Ethernet0/3
 switchport access vlan 999
 switchport mode access
 shutdown 
!         
interface Vlan10
 ip address 192.168.10.12 255.255.255.0
!         
ip default-gateway 192.168.10.1
ip forward-protocol nd
!         
no ip http server
no ip http secure-server
!         
!         
!         
!         
!         
!         
control-plane
!         
banner motd ^CAuthentification is nessesary^C
!         
line con 0
 password 7 060506324F41
 logging synchronous
 login    
line aux 0
line vty 0 4
 password 7 121A0C041104
 login    
 transport input ssh
!         
!         
end       
```

