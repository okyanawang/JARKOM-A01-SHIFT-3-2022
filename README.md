# Jarkom-Shift-Modul-3-A01-2022

### Anggota Kelompok A01

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

================================================================
Run : - (Already set on /root/.bashrc)
/================================================================

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
<img width="481" alt="topology" src="https://user-images.githubusercontent.com/32472207/201685364-547b9dd3-cd6d-499f-b3ba-30fbb0128005.png">

## Nomer 2
dan Ostania sebagai DHCP Relay

================================================================
Run : - (Already set on /root/.bashrc)
/================================================================

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

================================================================
Run : 
[On Westalis]
bash /root/file-3.sh
/================================================================

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
<img width="372" alt="no34" src="https://user-images.githubusercontent.com/32472207/201685844-e4fea7c6-a2bd-4ba3-961e-92467c4a3da7.png">


## Nomer 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85

================================================================
Run :
[On Westalis]
bash /root/file-4.sh
/================================================================

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
<img width="372" alt="no34" src="https://user-images.githubusercontent.com/32472207/201685960-a1933556-b237-45e5-b4d7-aa9fc1c34628.png">


## Nomer 5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut

================================================================
Run :
[On Wise]
bash /root/file-5.sh
/================================================================

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

================================================================
Run :
[On Westalis]
bash /root/file-6.sh
[On Ostania]
bash /root/file-6.sh
/================================================================

> Konfigurasi sudah dimasukkan pada nomor 3 & 4
> Testing on SSS
```
Ping 8.8.8.8
```
<img width="283" alt="no6" src="https://user-images.githubusercontent.com/32472207/201686159-e21ee01e-a91c-44ce-b93a-d84330c9c671.png">


## Nomer 7
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13 

================================================================
Run :
[On Westalis]
bash /root/file-7.sh
/================================================================

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
<img width="374" alt="no7" src="https://user-images.githubusercontent.com/32472207/201686306-1e07be6a-e535-4c69-8708-fb5805d020cc.png">
<img width="338" alt="no7t" src="https://user-images.githubusercontent.com/32472207/201686334-c94f9bc5-2bff-4232-a844-61c62ffd8b2f.png">


## Nomer 1 Proxy
Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)

================================================================
Run :
[On Berlint]
bash /root/file-px-1.sh
[On SSS] (Testing)
bash /root/file-px-1.sh
/================================================================

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
<img width="385" alt="px-1" src="https://user-images.githubusercontent.com/32472207/201686938-907b3551-ef8b-4866-9f44-1f37a7be8a4d.png">
<img width="571" alt="px-1t1" src="https://user-images.githubusercontent.com/32472207/201686949-7b58ad62-ae37-42a1-8983-29f08c7450d1.png">
<img width="511" alt="px-1t2" src="https://user-images.githubusercontent.com/32472207/201686959-e4e752c3-c314-42b0-8aa6-80d35ef8c0c2.png">


## Nomer 2 Proxy
Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)

================================================================
Run :
[On Berlint]
bash /root/file-px-2.sh
[On SSS](Testing)
bash /root/file-px-2.sh
/================================================================

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
<img width="710" alt="px-2" src="https://user-images.githubusercontent.com/32472207/201686987-6b13eee1-8d3a-437b-9f6b-0757a16b2b12.png">
<img width="488" alt="px-2t1" src="https://user-images.githubusercontent.com/32472207/201687007-2d185f7e-30f6-4e68-86f7-34b699cbc806.png">
<img width="477" alt="px-2t2" src="https://user-images.githubusercontent.com/32472207/201687042-40ad0a66-9b27-4097-822f-329cf6ad3755.png">

## Nomer 3 Proxy
Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)

================================================================
Run :
[On Berlint]
bash /root/file-px-3.sh
[On SSS](Testing)
bash /root/file-px-3.sh
/================================================================

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
<img width="611" alt="px-3" src="https://user-images.githubusercontent.com/32472207/201687089-c4a37f79-279e-421a-85f4-ccd091ae83f9.png">
<img width="407" alt="px-3t1" src="https://user-images.githubusercontent.com/32472207/201687102-87c48cc3-63c8-4602-b608-8a443d1fa17c.png">
<img width="383" alt="px-3t2" src="https://user-images.githubusercontent.com/32472207/201687112-d7dff9d7-e91a-41d8-a333-47fa39840fbb.png">


## Nomer 4 Proxy
Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)

================================================================
Run :
[On Berlint]
bash /root/file-px-4.sh
[On SSS & Garden](Testing)
bash /root/file-px-4.sh
/================================================================

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
<img width="703" alt="px-4" src="https://user-images.githubusercontent.com/32472207/201687138-d02cd752-9289-4c9f-89d6-19a03017f279.png">
<img width="286" alt="px-4t1" src="https://user-images.githubusercontent.com/32472207/201687156-3c20831a-7e23-459b-9269-d29f2171443b.png">


## Nomer 5 Proxy
Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

================================================================
Run :
[On Berlint]
bash /root/file-px-5.sh
[On SSS & Garden](Testing)
bash /root/file-px-5.sh
/================================================================

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
<img width="695" alt="px-5" src="https://user-images.githubusercontent.com/32472207/201687185-22057081-03ed-4b86-9f23-b0b5d3ba76d8.png">
<img width="312" alt="px-5t1" src="https://user-images.githubusercontent.com/32472207/201687207-c11f066d-4dcf-45c4-88e6-36260f2c658f.png">


## Kendala
- Tidak ada materi DHCP Relay di modul!
- Pada nomor 2 Proxy, di minta testing url yang tidak ada, alias harus mengkonfigurasi dns dan web server sendiri
- Bisa dilihat apakah ada sesuatu yang salah dengan repo ini, monggo di cek, karena ada
