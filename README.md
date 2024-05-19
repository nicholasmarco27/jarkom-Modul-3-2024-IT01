# Laporan Resmi Jarkom Modul 3
## Kelompok IT01
| NRP | Nama |
| ------ | ------ |
| 5027221042 |Nicholas Marco Weinandra |
| 5027221052 | Mochamad Zidan Hadipratama |

# Topologi
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/e232f273-a6a0-45cf-a2ba-535dbff73aa6)

# Konfigurasi Network
## Arakis (DHCP Relay)
```
# Static config for eth0
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.64.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.64.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.64.3.1
	netmask 255.255.255.0

auto eth4
iface eth4 inet static
	address 10.64.4.1
	netmask 255.255.255.0
```
## Mohiam (DHCP Server)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.3.3
	netmask 255.255.255.0
	gateway 10.64.3.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Irulan (DNS Server)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.3.2
	netmask 255.255.255.0
	gateway 10.64.3.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Dmitri & Paul (Client)
```
# DHCP config for eth0
auto eth0
iface eth0 inet dhcp
```
## Vladimir (PHP Worker)
```
auto eth0
iface eth0 inet static
	address 10.64.1.2
	netmask 255.255.255.0
	gateway 10.64.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Rabban (PHP Worker)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.1.3
	netmask 255.255.255.0
	gateway 10.64.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Feyd (PHP Worker)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.1.4
	netmask 255.255.255.0
	gateway 10.64.1.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Leto (Laravel Worker)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.2.2
	netmask 255.255.255.0
	gateway 10.64.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Duncan (Laravel Worker)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.2.3
	netmask 255.255.255.0
	gateway 10.64.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Jessica (Laravel Worker)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.2.4
	netmask 255.255.255.0
	gateway 10.64.2.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Stilgar (Load Balancer)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.4.3
	netmask 255.255.255.0
	gateway 10.64.4.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
## Chani (Database Server)
```
# Static config for eth0
auto eth0
iface eth0 inet static
	address 10.64.4.2
	netmask 255.255.255.0
	gateway 10.64.4.1
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
