# Laporan Resmi Jarkom Modul 3
## Kelompok IT01
| NRP | Nama |
| ------ | ------ |
| 5027221042 |Nicholas Marco Weinandra |
| 5027221052 | Mochamad Zidan Hadipratama |

## Daftar Isi
- [Soal 0](#soal-0)


# Topologi
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/e232f273-a6a0-45cf-a2ba-535dbff73aa6)

# Setup
## Arakis
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.64.0.0/16

apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
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
- Restart bind9 dengan command `service bind9 restart`

## Testing
- ping atreides.it01.com
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/b49a253e-a5f0-48c1-97d6-61d705d63dfa)
- ping harkonen.it01.com
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/01ddb588-6e82-4b74-99d1-e9c0f9e5b213)

# Soal 1
> Lakukan konfigurasi sesuai dengan peta yang sudah diberikan. Semua CLIENT harus menggunakan konfigurasi dari DHCP Server.

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

# Soal 2 & 3
> Client yang melalui House Harkonen mendapatkan range IP dari [prefix IP].1.14 - [prefix IP].1.28 dan [prefix IP].1.49 - [prefix IP].1.70 (2)
> Client yang melalui House Atreides mendapatkan range IP dari [prefix IP].2.15 - [prefix IP].2.25 dan [prefix IP].2 .200 - [prefix IP].2.210 (3)

## Node Mohiam
- Setelah melakukan setup, edit konfigurasi file `/etc/default/isc-dhcp-server`
```
INTERFACESv4="eth0"
```
- Kemudian, edit file konfigurasi `/etc/dhcp/dhcpd.conf` sesuai dengan perintah soal. Prefix IP kelompok kami adalah 10.64
```
# Default lease time dan max lease time
default-lease-time 300; # 5 menit

# Subnet untuk House Harkonen
subnet 10.64.1.0 netmask 255.255.255.0 {
    range 10.64.1.14 10.64.1.28;
    range 10.64.1.49 10.64.1.70;
    option routers 10.64.1.1;
    option broadcast-address 10.64.1.255;
    option domain-name-servers 10.64.3.2;
    default-lease-time 300; # 5 menit
    max-lease-time 5220; 
}

# Subnet untuk House Atreides
subnet 10.64.2.0 netmask 255.255.255.0 {
    range 10.64.2.15 10.64.2.25;
    range 10.64.2.200 10.64.2.210;
    option routers 10.64.2.1;
    option broadcast-address 10.64.2.255;
    option domain-name-servers 10.64.3.2;
    default-lease-time 1200; # 20 menit
    max-lease-time 5220; 
}

subnet 10.64.3.0 netmask 255.255.255.0{}
subnet 10.64.4.0 netmask 255.255.255.0{}
```
- Restart DHCP Server dengan menggunakan command `service isc-dhcp-server restart`

# Soal 4
> Client mendapatkan DNS dari Princess Irulan dan dapat terhubung dengan internet melalui DNS tersebut

## Node Irulan
- Setelah melakukan setup pada Irulan, edit konfigurasi file `/etc/bind/named.conf.options` agar client dapat terhubung dengan internet
```
options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
        };

        //========================================================================
        // If BIND logs error messages about the root key being expired,
        // you will need to update your keys.  See https://www.isc.org/bind-keys
        //========================================================================
        //dnssec-validation auto;
        allow-query { any; };
        auth-nxdomain no;
        listen-on-v6 { any; };
};
```
- Selanjutnya, kita perlu melakukan setting pada DHCP Relay.
## Node Arakis
- Setelah melakukan setup pada Arakis, edit konfigurasi file `/etc/default/isc-dhcp-relay`
```
# IP DHCP Server -> IP Mohiam
SERVERS="10.64.3.3"
# Interfaces to listen on
INTERFACES="eth1 eth2 eth3 eth4"
# Options to pass to the DHCP relay
OPTIONS=""
```
- Lalu, restart DHCP Relay dengan command `service isc-dhcp-relay restart`

## Testing
- Ping google.com dari Client
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/81f81a21-f777-4bb7-8563-ae51cc10136c)
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/6ae4c516-3001-4112-b89b-9b9d54bf0fcf)

# Soal 5
> Durasi DHCP server meminjamkan alamat IP kepada Client yang melalui House Harkonen selama 5 menit sedangkan pada client yang melalui House Atreides selama 20 menit. Dengan waktu maksimal dialokasikan untuk peminjaman alamat IP selama 87 menit

## Node Irulan
- Edit file konfigurasi `/etc/dhcp/dhcpd.conf` sesuai dengan perintah soal.
```
# Default lease time dan max lease time
default-lease-time 300; # 5 menit

# Subnet untuk House Harkonen
subnet 10.64.1.0 netmask 255.255.255.0 {
    range 10.64.1.14 10.64.1.28;
    range 10.64.1.49 10.64.1.70;
    option routers 10.64.1.1;
    option broadcast-address 10.64.1.255;
    option domain-name-servers 10.64.3.2;
    default-lease-time 300; # 5 menit
    max-lease-time 5220; 
}

# Subnet untuk House Atreides
subnet 10.64.2.0 netmask 255.255.255.0 {
    range 10.64.2.15 10.64.2.25;
    range 10.64.2.200 10.64.2.210;
    option routers 10.64.2.1;
    option broadcast-address 10.64.2.255;
    option domain-name-servers 10.64.3.2;
    default-lease-time 1200; # 20 menit
    max-lease-time 5220; 
}

subnet 10.64.3.0 netmask 255.255.255.0{}
subnet 10.64.4.0 netmask 255.255.255.0{}
```
- Restart DHCP Server dengan menggunakan command `service isc-dhcp-server restart`

# Soal 6
> Vladimir Harkonen memerintahkan setiap worker(harkonen) PHP, untuk melakukan konfigurasi virtual host untuk website berikut dengan menggunakan php 7.3.
## Node Vladimir, Rabban, Feyd
- Setelah melakukan setup, kita perlu download terlebih dahulu file website menggunakan `wget`, kemudian melakukan unzip
```
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1lmnXJUbyx1JDt2OA5z_1dEowxozfkn30' -O /var/www/harkonen.com
unzip -o /var/www/harkonen.com -d /var/www/
rm /var/www/harkonen.com
mv /var/www/modul-3 /var/www/harkonen.com
```
- Kemudian lakukan konfigurasi pada nginx sebagai berikut
```
cp /etc/nginx/sites-available/default /etc/nginx/sites-available/harkonen.com
ln -s /etc/nginx/sites-available/harkonen.com /etc/nginx/sites-enabled/
rm /etc/nginx/sites-enabled/default

echo 'server {
    listen 80;
    server_name _;

    root /var/www/harkonen.com;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/run/php/php7.3-fpm.sock; 
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}' > /etc/nginx/sites-available/harkonen.com

service nginx restart
```

## Testing
- Jalankan command `lynx localhost` pada PHP Worker
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/3dd9b328-5f45-4e83-9e4a-9748953e7c60)
