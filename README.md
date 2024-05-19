# Laporan Resmi Jarkom Modul 3
## Kelompok IT01
| NRP | Nama |
| ------ | ------ |
| 5027221042 |Nicholas Marco Weinandra |
| 5027221052 | Mochamad Zidan Hadipratama |

# Topologi
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/e232f273-a6a0-45cf-a2ba-535dbff73aa6)

# Setup
## Arakis
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.64.0.0/16

apt-get update
apt-get install isc-dhcp-relay -y
```
## Irulan
```
# Install bind9
apt-get update
apt-get install -y bind9
```
## Mohiam
```
apt-get update
apt-get install isc-dhcp-server
```
## Feyd, Vladimir, Rabban
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install nginx -y
apt-get install wget -y
apt-get install unzip -y
apt-get install lynx -y
apt-get install htop -y
apt-get install apache2-utils -y
apt-get install php7.3-fpm php7.3-common php7.3-mysql php7.3-gmp php7.3-curl php7.3-intl php7.3-mbstring php7.3-xmlrpc php7.3-gd php7.3-xml php7.3-cli php7.3-zip -y
apt update
apt install wget

service nginx start
service php7.3-fpm start
```


# Soal 0
> Planet Caladan sedang mengalami krisis karena kehabisan spice, klan atreides berencana untuk melakukan eksplorasi ke planet arakis dipimpin oleh duke leto mereka meregister domain name atreides.yyy.com untuk worker Laravel mengarah pada Leto Atreides . Namun ternyata tidak hanya klan atreides yang berusaha melakukan eksplorasi, Klan harkonen sudah mendaftarkan domain name harkonen.yyy.com untuk worker PHP (0) mengarah pada Vladimir Harkonen

## Server Irulan
- Setelah melakukan setup, edit file `/etc/bind/named.conf.local`
```
zone "atreides.it01.com" {
  type master;
  file "/etc/bind/atreides/atreides.it01.com";
};

zone "harkonen.it01.com" {
  type master;
  file "/etc/bind/harkonen/harkonen.it01.com";
};
```
- Buat direktori baru untuk masing-masing domain
```
mkdir /etc/bind/atreides
mkdir /etc/bind/harkonen
```
- Copy file db.local ke file konfigurasi domain
```
cp /etc/bind/db.local /etc/bind/atreides/atreides.it01.com
cp /etc/bind/db.local /etc/bind/harkonen/harkonen.it01.com
```
- Edit file `/etc/bind/atreides/atreides.it01.com`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     atreides.it01.com. root.atreides.it01.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      atreides.it01.com.
@       IN      A       10.64.2.2 ; IP Leto Atreides
@       IN      AAAA    ::1
```
- Edit file `/etc/bind/harkonen/harkonen.it01.com`
```
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     harkonen.it01.com. root.harkonen.it01.com. (
                              2         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      harkonen.it01.com.
@       IN      A       10.64.1.2 ; IP Vladimir Harkonen
@       IN      AAAA    ::1
```
## Testing
- ping atreides.it01.com
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/b49a253e-a5f0-48c1-97d6-61d705d63dfa)
- ping harkonen.it01.com
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/01ddb588-6e82-4b74-99d1-e9c0f9e5b213)

# Soal 1
> Lakukan konfigurasi sesuai dengan peta yang sudah diberikan.

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
