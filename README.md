# Jarkom-Shift-Modul-3-A01-2022

### Kelompok A01

| **No** | **Nama** | **NRP** | 
| ------------- | ------------- | --------- |
| 1 | Fachrendy Zulfikar Abdillah  | 5025201018 | 
| 2 | Muhammad Dzikri Fakhrizal Syairozi | 5025201201 |
| 3 | Okyan Awang Ramadhana | 5025201252 |

https://docs.google.com/document/d/1XSi9DiFPC4Gk1OcA5WWhwUXXY3q0ZbRQIazb8687jsU/edit?usp=sharing

## Daftar isi

- [Anggota Kelompok](#anggota-kelompok)
- [Nomer 1](#nomer-1)
- [Nomer 2](#nomer-2)
- [Nomer 3](#nomer-3)
- [Nomer 4](#nomer-4)
- [Nomer 5](#nomer-5)
- [Nomer 6](#nomer-6)
- [Nomer 7](#nomer-7)
- [Nomer 1 Proxy](#nomer-px-1)
- [Nomer 2 Proxy](#nomer-px-2)
- [Nomer 3 Proxy](#nomer-px-3)
- [Nomer 4 Proxy](#nomer-px-4)
- [Nomer  Proxy5](#nomer-px-5)
- [Kendala](#kendala)

## Nomer 1
Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server
- DNS Server : WISE
- DHCP Server : Westalis
- Proxy Server : Berlint
- DHCP Relay : Ostania
- Client : SSS & Garden

> Dalam terminal WISE
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```

> Dalam terminal Westalis
```
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-server -y
dhcpd --version
```

> Dalam terminal Berlint
```
apt-get update
apt-get install squid -y
service squid start
```


## Nomer 2
dan Ostania sebagai DHCP Relay

> Dalam terminal OSTANIA
```
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
echo 'nameserver 192.168.122.1' > /etc/resolv.conf
apt-get update
apt-get install isc-dhcp-relay -y
service isc-dhcp-relay start
echo '
SERVERS="192.169.2.4"
INTERFACES="eth1 eth2 eth3"
OPTIONS=
' > /etc/default/isc-dhcp-relay
echo '
net.ipv4.ip_forward=1
' > /etc/sysctl.conf
```


## Nomer 3
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155

> Dalam terminal Westalis
```
echo '
subnet 192.169.2.0 netmask 255.255.255.0 {

}
subnet 192.169.1.0 netmask 255.255.255.0 {
    range 192.169.1.50 192.169.1.88;
    range 192.169.1.120 192.169.1.155;
    option routers 192.169.1.1;
    option broadcast-address 192.169.1.255;
    option domain-name-servers 192.169.2.2;
    default-lease-time 300;
    max-lease-time 6900;
}
' >> /etc/dhcp/dhcpd.conf
```


## Nomer 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85

> Dalam terminal Westalis
```
echo '
subnet 192.169.3.0 netmask 255.255.255.0 {
    range 192.169.3.10 192.169.3.30;
    range 192.169.3.60 192.169.3.85;
    option routers 192.169.3.1;
    option broadcast-address 192.169.3.255;
    option domain-name-servers 192.169.2.2;
    default-lease-time 600;
    max-lease-time 6900;
}
' >> /etc/dhcp/dhcpd.conf
```


## Nomer 5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut

> Dalam terminal WISE
```
echo '
options {
        directory "/var/cache/bind";

        forwarders {
                192.168.122.1;
         };
        dnssec-validation auto;

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options
```


## Nomer 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit

> Konfigurasi sudah dimasukkan pada nomor 3 & 4
> Testing on SSS
```
Ping 8.8.8.8
```


## Nomer 7
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13 

> Dalam terminal Westalis
```
echo '
host Eden {
    hardware ethernet 76:96:e4:25:a3:99;
    fixed-address 192.169.3.13;
}
' >> /etc/dhcp/dhcpd.conf

service isc-dhcp-server restart
```

Testing
> On Eden, Stop node and restart it, then match it with picture below


## Nomer 1 Proxy
Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)

> Dalam terminal Berlinr
```
echo '
acl WORKING time MTWHF 08:00-17:00
' > /etc/squid/acl.conf
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

http_access deny WORKING
http_access allow all
' > /etc/squid/squid.conf

service squid restart
```

> Dalam terminal SSS untuk Testing
```
echo 'nameserver 192.168.122.1' >> /etc/resolv.conf
unset http_proxy
apt install lynx -y
export http_proxy="http://192.169.2.3:8080"
date -s "7 nov 2022 13:00"
lynx google.com
date -s "7 nov 2022 18:00"
lynx google.com
```

## Nomer 2 Proxy
Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)

> Dalam terminal Berlint
```
echo '
loid-work.com
franky-work.com
' > /etc/squid/work-sites.acl

echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access allow WORKSITES
http_access deny WORKING
http_access allow all
' > /etc/squid/squid.conf

service squid restart 
```

Untuk mengetest hasilnya
> Dalam terminal SSS
```
export http_proxy="http://192.169.2.3:8080"

date -s "7 nov 2022 13:00"
lynx google.com
lynx loid-work.com
lynx franky-work.com
```

## Nomer 3 Proxy
Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)

> Dalam terminal Berlint
```
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl SSL_ports port 443
acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access deny !SSL_ports
http_access allow WORKSITES
http_access deny WORKING
http_access allow all
' > /etc/squid/squid.conf

service squid restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
export http_proxy="http://192.169.2.3:8080"
date -s "7 nov 2022 18:00"
lynx http://example.com
lynx https://example.com
```


## Nomer 4 Proxy
Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)

> Dalam terminal Berlint
```
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl SSL_ports port 443
acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
#http_access deny !SSL_ports
http_access allow WORKSITES
#http_access deny WORKING
http_access allow all

delay_pools 1
delay_class 1 2
delay_access 1 allow all
delay_parameters 1 none 16000/16000
' > /etc/squid/squid.conf
service squid restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
unset http_proxy

apt install speedtest-cli

export PYTHONHTTPSVERIFY=0
export http_proxy="http://192.169.2.3:8080"

date -s "12 nov 2022 19:00"
```


## Nomer 5 Proxy
Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

> Dalam terminal Berlint
```
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl SSL_ports port 443
acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
#http_access deny !SSL_ports
http_access allow WORKSITES
#http_access deny WORKING
http_access allow all

Acl OPEN_TIME time MTWHF
delay_pools 1
delay_class 1 2
delay_access 1 allow !OPEN_TIME
delay_parameters 1 none 16000/16000
' > /etc/squid/squid.conf
service squid restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
unset http_proxy

apt install speedtest-cli

export PYTHONHTTPSVERIFY=0
export http_proxy="http://192.169.2.3:8080"

date -s "12 nov 2022 19:00"
```

## Kendala
- Tidak ada materi DHCP Relay di modul!
- Pada nomor 2 Proxy, di minta testing url yang tidak ada, alias harus mengkonfigurasi dns dan web server sendiri
- Bisa dilihat apakah ada sesuatu yang salah dengan repo ini, monggo di cek, karena ada
