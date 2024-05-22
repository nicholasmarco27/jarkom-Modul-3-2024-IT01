# Laporan Resmi Jarkom Modul 3
## Kelompok IT01
| NRP | Nama |
| ------ | ------ |
| 5027221042 |Nicholas Marco Weinandra |
| 5027221052 | Mochamad Zidan Hadipratama |

## Daftar Isi
- [Soal 0](#soal-0)
- [Soal 1](#soal-1)
- [Soal 2 & 3](#soal-2-&-3)
- [Soal 4](#soal-4)
- [Soal 5](#soal-5)
- [Soal 6](#soal-6)
- [Soal 7](#soal-7)
- [Soal 8](#soal-8)
- [Soal 9](#soal-9)
- [Soal 10](#soal-10)
- [Soal 11](#soal-11)
- [Soal 12](#soal-12)
- [Soal 13](#soal-13)
- [Soal 14](#soal-14)
- [Soal 15](#soal-15)
- [Soal 16](#soal-16)
- [Soal 17](#soal-17)
- [Soal 18](#soal-18)
- [Soal 19](#soal-19)
- [Soal 20](#soal-20)


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

# Soal 7
> Aturlah agar Stilgar dari fremen dapat dapat bekerja sama dengan maksimal, lalu lakukan testing dengan 5000 request dan 150 request/second
## Node Stilgar
- Install Setup
```
apt-get update
apt-get install nginx -y
service nginx stop
apt-get install apache2 -y
a2enmod proxy
a2enmod proxy_http
a2enmod proxy_balancer
a2enmod lbmethod_byrequests
a2enmod lbmethod_bybusyness
a2enmod lbmethod_bytraffic
apt-get install apache2-utils -y
```
## Konfigurasi Load Balancer pada
```
echo "===============Setup Load Balancer Apache2 default====================="
cat << 'EOF' > /etc/apache2/sites-available/000-default.conf
<VirtualHost *:80>
    <Proxy balancer://mycluster>
        BalancerMember http://10.64.1.2
        BalancerMember http://10.64.1.3
        BalancerMember http://10.64.1.4
    </Proxy>
    ProxyPass / balancer://mycluster/
    ProxyPassReverse / balancer://mycluster/
</VirtualHost>
EOF
```
## Testing
- Jalankan `ab -n 5000 -c 150 http://10.64.4.3/`
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/32287f6b-efb0-4203-977f-4d6d086f48f0)
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/9a577801-b77e-4923-b594-b082c87c9070)

# Soal 8
> Karena diminta untuk menuliskan peta tercepat menuju spice, buatlah analisis hasil testing dengan 500 request dan 50 request/second masing-masing algoritma Load Balancer dengan ketentuan sebagai berikut:
Nama Algoritma Load Balancer
Report hasil testing pada Apache Benchmark
Grafik request per second untuk masing masing algoritma. 
Analisis
- Setup Load Balancer Least Conn
```
echo "===============Setup Load Balancer NGINX Least Connections====================="
cat << 'EOF' > /etc/nginx/sites-available/default
upstream backend {
    least_conn;
    server 10.64.1.2;
    server 10.64.1.3;
    server 10.64.1.4;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend;
    }
}
EOF
```
- SEtup Load Balancer Round Robin
```
echo "===============Setup Load Balancer NGINX Round Robin====================="
cat << 'EOF' > /etc/nginx/sites-available/default
upstream backend {
    server 10.64.1.2;
    server 10.64.1.3;
    server 10.64.1.4;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend/;
    }
}
EOF
service nginx start
```
- Setup Load Balancer IP Hash
```
echo "===============Setup Load Balancer NGINX IP Hash====================="
cat << 'EOF' > /etc/nginx/sites-available/default
upstream backend {
    ip_hash;
    server 10.64.1.2;
    server 10.64.1.3;
    server 10.64.1.4;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend/index.php;
    }
}
EOF
service nginx restart
```
- Setup Load Balancer Gen Hash
```
echo "===============Setup Load Balancer NGINX Generic Hash====================="
cat << 'EOF' > /etc/nginx/sites-available/default
upstream backend {
    hash $request_uri consistent;
    server 10.64.1.2;
    server 10.64.1.3;
    server 10.64.1.4;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend/index.php;
    }
}
EOF
service nginx restart
```
## Testing
- Round Robin
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/5794a757-b7b5-4a96-9ac8-214b3d57eeaa)
- IP Hash
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/3fab12e5-14f7-4a3b-bb7a-8a192e86b965)
- Least Conn
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/95fed44c-d899-46df-b435-a154a27b34e7)
- Generic Hash
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/080dd89d-9de8-4c9f-8496-b9e5db86ecb7)

# Soal 8
## Analisis
Analisis dapat dilihat pada file berikut [IT01_Spice.pdf](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/files/15398388/IT01_Spice.pdf)


# Soal 9
> Dengan menggunakan algoritma Least-Connection, lakukan testing dengan menggunakan 3 worker, 2 worker, dan 1 worker sebanyak 1000 request dengan 10 request/second, kemudian tambahkan grafiknya pada peta.
- Konfigurasi Endpoints
```
cat << 'EOF' > /etc/nginx/sites-available/default
upstream backend {
    least_conn;
    server 10.64.1.2;
    server 10.64.1.3;
    server 10.64.1.4;
    # server 10.64.2.4;
    # server 10.64.2.3;
    # server 10.64.2.2;
}

server {
    listen 80;

    # Apply IP whitelist to all locations
    location / {

        # Enable Basic Authentication
        # auth_basic "Restricted Area";
        # auth_basic_user_file /etc/nginx/supersecret/htpasswd;

        # Proxy settings
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /dune {

        # Redirect to the external site using HTTPS
        proxy_pass https://www.dunemovie.com.au/;
        proxy_set_header Host www.dunemovie.com.au;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Optionally, to make sure the URL path is preserved
        proxy_redirect off;
    }
}
EOF
```
- Restart nginx `service nginx restart`
## Testing
- 3 Worker
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/8094650d-b537-4f2c-8d5d-b7963c30a012)
- 2 Worker
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/22ae9f0c-ed19-4b1d-b69f-92267d8e4d60)
- 1 Worker
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/93b5eb6b-b405-4e2e-927f-b4965e41e7b5)

# Soal 10
> Selanjutnya coba tambahkan keamanan dengan konfigurasi autentikasi di LB dengan dengan kombinasi username: “secmart” dan password: “kcksyyy”, dengan yyy merupakan kode kelompok. Terakhir simpan file “htpasswd” nya di /etc/nginx/supersecret/ 
- Buat direktori supersecret
```
mkdir /etc/nginx/supersecret
```
- Jalankan command `htpasswd` dan set username `secmart` dan password `kcksit01`
```
htpasswd -c /etc/nginx/supersecret/htpasswd secmart
```
## Testing
- Masuk ke `lynx 10.64.4.3`, isi username `secmart` dan pass `kcksit01`
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/648ca425-fdc8-4355-905a-86a35049c7a9)
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/3dfb33cb-ae9b-4ed2-8730-3510bbfddc01)
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/e5f39eda-16f2-4ed9-a7d7-38b5c4ca853d)

# Soal 11
> Lalu buat untuk setiap request yang mengandung /dune akan di proxy passing menuju halaman https://www.dunemovie.com.au/.
- Setiap request akan di proxy passing ke www.dunemovie.com.au
```
cat << 'EOF' > /etc/nginx/sites-available/default
# Define IP whitelist in a reusable map
map $remote_addr $ip_allowed {
    default 0;
    10.64.1.37 1;
    10.64.1.67 1;  # Example IP address allowed to access
    10.64.2.203 1;
    10.64.2.207 1;  # Another example IP address allowed to access
    # Add more IP addresses as needed
}

upstream backend {
    least_conn;
    server 10.64.1.2;
    server 10.64.1.3;
    server 10.64.1.4;
}

server {
    listen 80;

    # Apply IP whitelist to all locations
    location / {
        # Allow only specific IP addresses
        if ($ip_allowed = 0) {
            return 403;  # Forbidden
        }

        # Enable Basic Authentication
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/supersecret/htpasswd;

        # Proxy settings
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /dune {
        # Apply IP whitelist to /dune location
        if ($ip_allowed = 0) {
            return 403;  # Forbidden
        }

        # Redirect to the external site using HTTPS
        proxy_pass https://www.dunemovie.com.au/;
        proxy_set_header Host www.dunemovie.com.au;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Optionally, to make sure the URL path is preserved
        proxy_redirect off;
    }
}
EOF
service nginx restart
```
# Testing
- Akses `lynx 10.64.4.3/dune`
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/26c8bc9f-979d-4f2c-a6d2-b15c9753d564)

# Soal 12
> Selanjutnya LB ini hanya boleh diakses oleh client dengan IP [Prefix IP].1.37, [Prefix IP].1.67, [Prefix IP].2.203, dan [Prefix IP].2.207
## Node Stilgar
- Konfigurasi fixed IP address
```
echo "===============Setup Load Balancer NGINX Round Robin====================="
cat << 'EOF' > /etc/nginx/sites-available/default
upstream backend {
    server 10.64.1.2;
    server 10.64.1.3;
    server 10.64.1.4;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend/;
    }
}
EOF
service nginx start
```
- Restart `service nginx restart`

## Testing
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/15050ed2-dbc5-449e-8625-88beb842375e)
- masuk ke website dari client

# Soal 13
> Semua data yang diperlukan, diatur pada Chani dan harus dapat diakses oleh Leto, Duncan, dan Jessica.
- Setup Chani
```
apt install mariadb-server -y
service mysql start
mysql -u root -p <<EOF
GRANT ALL PRIVILEGES ON *.* TO 'root'@'%' IDENTIFIED BY '123';
CREATE DATABASE laravel;
FLUSH PRIVILEGES;
EOF
cat << 'EOF' > /etc/mysql/my.cnf
# The MariaDB configuration file
#
# The MariaDB/MySQL tools read configuration files in the following order:
# 1. "/etc/mysql/mariadb.cnf" (this file) to set global defaults,
# 2. "/etc/mysql/conf.d/*.cnf" to set global options.
# 3. "/etc/mysql/mariadb.conf.d/*.cnf" to set MariaDB-only options.
# 4. "~/.my.cnf" to set user-specific options.
#
# If the same option is defined multiple times, the last one will apply.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.

#
# This group is read both both by the client and the server
# use it for options that affect everything
#
[client-server]

# Import all .cnf files from configuration directory
!includedir /etc/mysql/conf.d/
!includedir /etc/mysql/mariadb.conf.d/

[mysqld]
bind-address = 0.0.0.0
EOF
service mysql restart
ufw allow 3306
sudo iptables -A INPUT -p tcp --dport 3306 -j ACCEPT
service iptables save
sudo systemctl restart mysql
apt-get install ufw
ufw allow 3306
```
# Node Dmitri
```
mysql -u root -h 10.64.4.2 -p -P 3306
```

## Testing
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/822394fa-0eaf-4ba3-af75-baee329b98b1)


# Soal 14
> Leto, Duncan, dan Jessica memiliki atreides Channel sesuai dengan quest guide berikut. Jangan lupa melakukan instalasi PHP8.0 dan Composer
## Hasil
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/18794e63-f24c-4845-8bc8-81a6daf670e7)

# Soal 15-17
> atreides Channel memiliki beberapa endpoint yang harus ditesting sebanyak 100 request dengan 10 request/second. Tambahkan response dan hasil testing pada peta.
POST /auth/register (15)
POST /auth/log
in (16)
GET /me (17)
- File analisis dapat diakses di [IT01_Spice.pdf](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/files/15398428/IT01_Spice.pdf)

# Soal 18
> Untuk memastikan ketiganya bekerja sama secara adil untuk mengatur atreides Channel maka implementasikan Proxy Bind pada Stilgar untuk mengaitkan IP dari Leto, Duncan, dan Jessica
- Konfigurasi di Stilgar
```
cat << 'EOF' > /etc/nginx/sites-available/default


upstream backend {
    least_conn;
    server 10.64.1.2;
    server 10.64.1.3;
    server 10.64.1.4;
    server 10.64.2.4;
    server 10.64.2.3;
    server 10.64.2.2;
}

server {
    listen 80;

    # Apply IP whitelist to all locations
    location / {

        #Enable Basic Authentication
        auth_basic "Restricted Area";
        auth_basic_user_file /etc/nginx/supersecret/htpasswd;

        

        # Proxy settings
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /dune {
       

        # Redirect to the external site using HTTPS
        proxy_pass https://www.dunemovie.com.au/;
        proxy_set_header Host www.dunemovie.com.au;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

        # Optionally, to make sure the URL path is preserved
        proxy_redirect off;
    }
}
EOF
```

# Soal 19
> Untuk meningkatkan performa dari Worker, coba implementasikan PHP-FPM pada Leto, Duncan, dan Jessica. Untuk testing kinerja naikkan 
- pm.max_children
- pm.start_servers
- pm.min_spare_servers
- pm.max_spare_servers
sebanyak tiga percobaan dan lakukan testing sebanyak 100 request dengan 10 request/second kemudian berikan hasil analisisnya pada PDF.
- Hasil Testing : [IT01_Spice.pdf](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/files/15398473/IT01_Spice.pdf)

# Soal 20
>Nampaknya hanya menggunakan PHP-FPM tidak cukup untuk meningkatkan performa dari worker maka implementasikan Least-Conn pada Stilgar. Untuk testing kinerja dari worker tersebut dilakukan sebanyak 100 request dengan 10 request/second. 
- Install PHP FPM `apt-get install php-fpm -y`
- Melakukan konfigurasi berikut di setiap worker
![image](https://github.com/nicholasmarco27/jarkom-Modul-3-2024-IT01/assets/80316798/4e67f75d-860e-4151-9d9f-8d5c4fa0a799)

