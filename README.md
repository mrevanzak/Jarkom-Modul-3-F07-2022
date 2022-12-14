# JARKOM Soal Shift Modul 3

## Daftar isi

- [Anggota Kelompok](#anggota-kelompok)
- [Nomor 1](#nomor-1)
- [Nomor 2](#nomor-2)
- [Nomor 3](#nomor-3)
- [Nomor 4](#nomor-4)
- [Nomor 5](#nomor-5)
- [Nomor 6](#nomor-6)
- [Nomor 7](#nomor-7)
- [Nomor 1-Proxy](#nomor-1-proxy)
- [Nomor 2-Proxy](#nomor-2-proxy)
- [Nomor 3-Proxy](#nomor-3-proxy)
- [Nomor 4-Proxy](#nomor-4-proxy)
- [Nomor 5-Proxy](#nomor-5-proxy)

## Anggota Kelompok F07

| NRP        | NAMA                       |
| ---------- | -------------------------- |
| 5025201145 | Mochamad Revanza Kurniawan |
| 5025201208 | Bagus Febrian Dali Hidayat |
| 5025201241 | Jabalnur                   |

## Nomor 1
### Buat Topologi
- DNS Server : WISE
- DHCP Server : Westalis
- Proxy Server : Berlint
- DHCP Relay : Ostania
- Client Proxy : SSS, Garden, Eden
- Client : NewstonCastle, KemonoPark
- Prefix : 10.32

> Buat Topologi

![Screenshot from 2022-11-13 18-00-20](https://user-images.githubusercontent.com/60770478/201517888-42557db2-1216-4aa1-89da-dc9693d1adb2.png)

### Konfigurasi IP

> Konfigurasi Ostania
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.32.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.32.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.32.3.1
	netmask 255.255.255.0
```
> Konfigurasi SSS
```
auto eth0
iface eth0 inet static
        address 10.32.1.2
        netmask 255.255.255.0
        gateway 10.32.1.1
```

> Konfigurasi Garden
```
auto eth0
iface eth0 inet static
        address 10.32.1.3
        netmask 255.255.255.0
        gateway 10.32.1.1
```

> Konfigurasi WISE
```
auto eth0
iface eth0 inet static
        address 10.32.2.2
        netmask 255.255.255.0
        gateway 10.32.2.1
```

> Konfigurasi Berlint
```
auto eth0
iface eth0 inet static
        address 10.32.2.3
        netmask 255.255.255.0
        gateway 10.32.2.1
```

> Konfigurasi Westalis
```
auto eth0
iface eth0 inet static
        address 10.32.2.4
        netmask 255.255.255.0
        gateway 10.32.2.1
```

> Konfigurasi Eden
```
auto eth0
iface eth0 inet static
        address 10.32.3.2
        netmask 255.255.255.0
        gateway 10.32.3.1
```

> Konfigurasi NewstonCastle
```
auto eth0
iface eth0 inet static
        address 10.32.3.3
        netmask 255.255.255.0
        gateway 10.32.3.1
```

> Konfigurasi KemonoPark
```
auto eth0
iface eth0 inet static
        address 10.32.3.4
        netmask 255.255.255.0
        gateway 10.32.3.1
```

### Konfigurasi .bashrc
> Dalam terminal Ostania
Masukkan command berikut pada .bashrc
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.32.0.0/16
apt-get update
```

> Dalam terminal WISE, Berlint, dan Westalis
Masukkan command berikut pada .bashrc
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
```
### WISE
```
apt-get install bind9 -y
service bind9 start
```
![Screenshot from 2022-11-13 18-00-20](https://user-images.githubusercontent.com/60770478/201518306-d38e3feb-60e7-4334-a40f-adf0161540de.png)

### Westalis
```
apt-get install isc-dhcp-server -y
dhcpd --version

echo '
INTERFACES="eth0"
' > /etc/default/isc-dhcp-server
```
![Screenshot from 2022-11-13 18-01-50](https://user-images.githubusercontent.com/60770478/201518354-ed18b56a-785a-41a1-8675-2485084bb0f1.png)

### Berlint
```
apt-get install squid -y
service squid start
```
![Screenshot from 2022-11-13 18-03-04](https://user-images.githubusercontent.com/60770478/201518396-fdf3cefd-4c75-499c-afb1-eeb0f5f03c5d.png)

## Nomor 2
### Ostania
```
apt-get install isc-dhcp-relay -y
echo '
# Defaults for isc-dhcp-relay initscript
# sourced by /etc/init.d/isc-dhcp-relay
# installed at /etc/default/isc-dhcp-relay by the maintainer scripts

#
# This is a POSIX shell fragment
#

# What servers should the DHCP relay forward requests to?
SERVERS="10.32.2.4"

# On what interfaces should the DHCP relay (dhrelay) serve DHCP requests?
INTERFACES="eth1 eth2 eth3"

# Additional options that are passed to the DHCP relay daemon?
OPTIONS=""
' > /etc/default/isc-dhcp-relay
```
Akan muncul 3 pertanyaan, tekan saja enter untuk melanjutkan. Nilainya akan terisi setelah instalasi.
![Screenshot from 2022-11-13 18-05-00](https://user-images.githubusercontent.com/60770478/201518493-e0794011-98d3-4c44-a64b-3aa1ee85938b.png)

## Nomor 3
Client yang melalui Switch1 mendapatkan range IP dari [prefix IP].1.50 - [prefix IP].1.88 dan [prefix IP].1.120 - [prefix IP].1.155 

### Westalis
```
echo '
subnet 10.32.1.0 netmask 255.255.255.0 {
        range 10.32.1.50 10.32.1.88;
        range 10.32.1.120 10.32.1.155;
        option routers 10.32.1.1;
        option broadcast-address 10.32.1.255;
        option domain-name-servers 10.32.2.2;
}
subnet 10.32.2.0 netmask 255.255.255.0 {
}
' > /etc/dhcp/dhcpd.conf
service isc-dhcp-server restart
service isc-dhcp-server status
```
![Screenshot from 2022-11-13 18-06-37](https://user-images.githubusercontent.com/60770478/201518539-18bcada4-32d4-488b-b05f-eec7882d8078.png)

### SSS
```
echo '
#auto eth0
#iface eth0 inet static
#        address 10.32.1.2
#        netmask 255.255.255.0
#        gateway 10.32.1.1
auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces
```
### Garden
```
echo '
#auto eth0
#iface eth0 inet static
#        address 10.32.1.3
#        netmask 255.255.255.0
#        gateway 10.32.1.1
auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces
```
## Nomor 4
Client yang melalui Switch3 mendapatkan range IP dari [prefix IP].3.10 - [prefix IP].3.30 dan [prefix IP].3.60 - [prefix IP].3.85

### Westalis
```
echo '
subnet 10.32.1.0 netmask 255.255.255.0 {
        range 10.32.1.50 10.32.1.88;
        range 10.32.1.120 10.32.1.155;
        option routers 10.32.1.1;
        option broadcast-address 10.32.1.255;
        option domain-name-servers 10.32.2.2;
}
subnet 10.32.3.0 netmask 255.255.255.0 {
        range 10.32.3.10 10.32.3.30;
        range 10.32.3.60 10.32.3.85;
        option routers 10.32.3.1;
        option broadcast-address 10.32.3.255;
        option domain-name-servers 10.32.2.2;
}
subnet 10.32.2.0 netmask 255.255.255.0 {
}
' > /etc/dhcp/dhcpd.conf
service isc-dhcp-server restart
service isc-dhcp-server status
```
![Screenshot from 2022-11-13 18-09-09](https://user-images.githubusercontent.com/60770478/201518627-dae9b5cb-c935-4f34-a202-e495ae931c23.png)

### Eden
```
echo '
#auto eth0
#iface eth0 inet static
#        address 10.32.3.2
#        netmask 255.255.255.0
#        gateway 10.32.3.1
auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces
```
### NewstonCastle
```
echo '
#auto eth0
#iface eth0 inet static
#        address 10.32.3.3
#        netmask 255.255.255.0
#        gateway 10.32.3.1
auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces
```
### KemonoPark
```
echo '
#auto eth0
#iface eth0 inet static
#        address 10.32.3.4
#        netmask 255.255.255.0
#        gateway 10.32.3.1
auto eth0
iface eth0 inet dhcp
' > /etc/network/interfaces
```

## Nomor 5
Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut

### WISE
```
echo '
options {
        directory "/var/cache/bind";

        forwarders {
                8.8.8.8;
                8.8.8.4;
        };
        allow-query { any; };

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options

service bind9 restart
```
![Screenshot from 2022-11-13 18-10-55](https://user-images.githubusercontent.com/60770478/201518697-43e0b045-f060-4dac-9c8b-63874c42a51a.png)

## Nomor 6
Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit. 

### Westalis
```
echo '
subnet 10.32.1.0 netmask 255.255.255.0 {
        range 10.32.1.50 10.32.1.88;
        range 10.32.1.120 10.32.1.155;
        option routers 10.32.1.1;
        option broadcast-address 10.32.1.255;
        option domain-name-servers 10.32.2.2;
        default-lease-time 300;
        max-lease-time 6900;
}
subnet 10.32.3.0 netmask 255.255.255.0 {
        range 10.32.3.10 10.32.3.30;
        range 10.32.3.60 10.32.3.85;
        option routers 10.32.3.1;
        option broadcast-address 10.32.3.255;
        option domain-name-servers 10.32.2.2;
        default-lease-time 600;
        max-lease-time 6900;
}
subnet 10.32.2.0 netmask 255.255.255.0 {
}
' > /etc/dhcp/dhcpd.conf
service isc-dhcp-server restart
service isc-dhcp-server status
```
![Screenshot from 2022-11-13 18-13-53](https://user-images.githubusercontent.com/60770478/201518803-fa732789-3da6-455a-a0d0-11a462f84c2a.png)

### Ostania
```
service isc-dhcp-relay restart
```
### SSS, Garden, Eden, NewstonCastle, KemonoPark
Restart Node dan cek IP barunya
> SSS
![Screenshot from 2022-11-13 18-20-32](https://user-images.githubusercontent.com/60770478/201519061-6426d584-a3d9-4423-8839-219db8378b1b.png)

> Garden
![Screenshot from 2022-11-13 18-21-10](https://user-images.githubusercontent.com/60770478/201519081-a582c48f-01d6-486a-a792-5ed76ed6de81.png)

> Eden
![Screenshot from 2022-11-13 18-21-54](https://user-images.githubusercontent.com/60770478/201519099-5f5e2f18-defd-46ec-9b69-a22b3539bb5a.png)

> NewstonCastle
![Screenshot from 2022-11-13 18-22-12](https://user-images.githubusercontent.com/60770478/201519107-2f4e36f7-6583-4f82-87b8-727c51494fd6.png)

> KemonoPark
![Screenshot from 2022-11-13 18-22-29](https://user-images.githubusercontent.com/60770478/201519117-e529e68f-3914-4765-889b-a3eb279d9344.png)

Jalankan command berikut pada masing-masing Node
```
cat /etc/resolv.conf
```
Hasilnya akan menunjukkan IP menuju WISE yaitu : 10.32.2.2
![Screenshot from 2022-11-13 18-24-27](https://user-images.githubusercontent.com/60770478/201519194-e9915e3c-06b9-492d-8441-91db6b143e0a.png)

## Nomor 7
Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP [prefix IP].3.13 (

### Eden
```
ip a
```
![Screenshot from 2022-11-13 18-25-58](https://user-images.githubusercontent.com/60770478/201519242-6f377216-5225-4906-b0bd-8719ad4e2903.png)
Copy ethernet pada Eden dan untuk digunakan pada Westalis dan Eden, pada kasus ini adalah 52:ef:b3:d3:13:2a

### Westalis
Jalankan command berikut dan masukkan ethernet Eden
```
echo '
subnet 10.32.1.0 netmask 255.255.255.0 {
        range 10.32.1.50 10.32.1.88;
        range 10.32.1.120 10.32.1.155;
        option routers 10.32.1.1;
        option broadcast-address 10.32.1.255;
        option domain-name-servers 10.32.2.2;
        default-lease-time 300;
        max-lease-time 6900;
}
subnet 10.32.3.0 netmask 255.255.255.0 {
        range 10.32.3.10 10.32.3.30;
        range 10.32.3.60 10.32.3.85;
        option routers 10.32.3.1;
        option broadcast-address 10.32.3.255;
        option domain-name-servers 10.32.2.2;
        default-lease-time 600;
        max-lease-time 6900;
}
subnet 10.32.2.0 netmask 255.255.255.0 {
}
host Eden {
        hardware ethernet 52:ef:b3:d3:13:2a;
        fixed-address 10.32.3.13;
}
' > /etc/dhcp/dhcpd.conf
service isc-dhcp-server restart
```
![Screenshot from 2022-11-13 18-28-51](https://user-images.githubusercontent.com/60770478/201519353-2a849fd7-569d-43b6-a247-100209641a23.png)

### Ostania
```
service isc-dhcp-relay restart
```
### Eden
Jalankan command berikut dan masukkan ethernet Eden
```
echo '
#auto eth0
#iface eth0 inet static
#        address 10.32.3.2
#        netmask 255.255.255.0
#        gateway 10.32.3.1
auto eth0
iface eth0 inet dhcp

hwaddress ether 52:ef:b3:d3:13:2a
' > /etc/network/interfaces
```
Restart Node Eden dan cek IP terbarunya
![Screenshot from 2022-11-13 18-32-08](https://user-images.githubusercontent.com/60770478/201519509-f2b8a863-ac88-4018-bde0-333283bd2a41.png)

## Nomor 1 Proxy
Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)

### Berlint
Pastikan auto update waktu dan tanggal pada komputer kita mati agar dapat mengubah tanggal dan waktu pada terminal.
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
![Screenshot from 2022-11-13 18-34-55](https://user-images.githubusercontent.com/60770478/201649316-4a38809f-d3f1-4f67-8296-ffdafd996161.png)


### SSS
```
apt-get update
apt-get install lynx -y

export http_proxy="http://10.32.2.3:8080"

date -s "7 NOV 2022 13:30:00"
lynx google.com
```
![Screenshot from 2022-11-07 20-30-35](https://user-images.githubusercontent.com/60770478/201519674-0751b8b2-bc1a-44ba-9721-da7a46842f7e.png)

### Garden
```
apt-get update
apt-get install lynx -y

export http_proxy="http://10.32.2.3:8080"

date -s "7 NOV 2022 18:30:00"
lynx google.com
```
![Screenshot from 2022-11-08 01-30-29](https://user-images.githubusercontent.com/60770478/201519736-6b40d1a3-d93a-4712-97c3-71d10bfc523b.png)

## Nomor 2 Proxy
Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)

### WISE
```
echo '
zone "loid-work.com" {
        type master;
        file "/etc/bind/jarkom/loid-work.com";
};
zone "franky-work.com" {
        type master;
        file "/etc/bind/jarkom/franky-work.com";
};
' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     loid-work.com. root.loid-work.com. (
                     2022110901         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      loid-work.com.
@       IN      A       10.32.2.2
' > /etc/bind/jarkom/loid-work.com

echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     franky-work.com. root.franky-work.com. (
                     2022110902         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      franky-work.com.
@       IN      A       10.32.2.2
' > /etc/bind/jarkom/franky-work.com

service bind9 restart
```
![Screenshot from 2022-11-08 01-32-25](https://user-images.githubusercontent.com/60770478/201519850-bde6ba97-8e30-4fa8-926f-df94a703651c.png)

### Berlint
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
![Screenshot from 2022-11-08 01-34-05](https://user-images.githubusercontent.com/60770478/201519904-494a8acb-4aae-4145-995f-1a1e11af83f0.png)

### SSS
```
export http_proxy="http://10.32.2.3:8080"

date -s "7 NOV 2022 13:30:00"

lynx franky-work.com
lynx loid-work.com
lynx google.com
```
> franky-work.com bisa diakses
![Screenshot from 2022-11-07 20-31-01](https://user-images.githubusercontent.com/60770478/201519983-b3a7072e-ea5c-4763-9dab-d53740675978.png)

> loid-work.com bisa diakses
![Screenshot from 2022-11-07 20-30-07](https://user-images.githubusercontent.com/60770478/201520025-bb4832ea-87af-43e9-a8fb-997754de1367.png)

> internet tidak bisa diakses
![Screenshot from 2022-11-07 20-30-17](https://user-images.githubusercontent.com/60770478/201520048-729ae5c2-a944-49f3-a445-236c5c48fb92.png)

## Nomor 3 Proxy
Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)

### Berlint
```
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access allow WORKSITES
http_access deny WORKING
http_access deny all


' > /etc/squid/squid.conf

service squid restart
```
![Screenshot from 2022-11-08 01-30-57](https://user-images.githubusercontent.com/60770478/201520263-bf3fed31-4ac4-467e-89dc-eb56b40103dd.png)

### SSS
```
export http_proxy="http://10.32.2.3:8080"

date -s "7 NOV 2022 18:30:00"

lynx http://www.example.com
lynx https://www.example.com
```
> http://example.com tidak bisa diakses
![Screenshot from 2022-11-08 01-30-03-1](https://user-images.githubusercontent.com/60770478/201520293-597c753b-fa71-4baf-9c0f-4cd36cda4224.png)

> https://example.com bisa diakses
![Screenshot from 2022-11-08 01-30-10-1](https://user-images.githubusercontent.com/60770478/201520299-ad9a8490-94cd-476b-89eb-6039a23df312.png)

## Nomor 4 Proxy
Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)


### Berlint
```
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access allow WORKSITES
http_access deny WORKING
http_access allow all

delay_pools 1
delay_class 1 2 
delay_access 1 allow all
delay_parameters 1 none 16000/32000
' > /etc/squid/squid.conf

service squid restart
```
![proxy4berlint](https://user-images.githubusercontent.com/60770478/201641079-564e6c4e-7dab-492d-ad77-f117a17c8d8c.png)

### SSS
```
unset http_proxy

apt-get install speedtest-cli -y

export PYTHONHTTPSVERIFY=0
export http_proxy="http://10.32.2.3:8080"

date -s "7 NOV 2022 18:30:00"
speedtest
```
![sss](https://user-images.githubusercontent.com/60770478/201641342-271e356a-2be6-4dbe-a62c-c301ec8b5d55.png)

### Garden
```
unset http_proxy

apt-get install speedtest-cli -y

export PYTHONHTTPSVERIFY=0
export http_proxy="http://10.32.2.3:8080"

date -s "7 NOV 2022 18:30:00"
speedtest
```
![garden](https://user-images.githubusercontent.com/60770478/201641515-493e5cae-5421-4999-b9cd-436665785186.png)


## Nomor 5 Proxy
Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

### Berlint
```
echo '
include /etc/squid/acl.conf

http_port 8080
visible_hostname Berlint

acl WORKSITES dstdomain "/etc/squid/work-sites.acl"
http_access allow WORKSITES
http_access deny WORKING
http_access allow all

acl OPEN_TIME time MTWHF
delay_pools 1
delay_class 1 2
delay_access 1 allow !OPEN_TIME
delay_parameters 1 none 16000/32000
' > /etc/squid/squid.conf

service squid restart
```
![Screenshot from 2022-11-07 01-30-05](https://user-images.githubusercontent.com/60770478/201642111-46761b5d-50f2-435c-868a-bcb1d560b1b0.png)

### SSS
```
unset http_proxy

export PYTHONHTTPSVERIFY=0
export http_proxy="http://10.32.2.3:8080"

date -s "6 NOV 2022 18:30:00"
speedtest
```
![Screenshot from 2022-11-07 01-37-16](https://user-images.githubusercontent.com/60770478/201642054-6a41f6eb-19e3-43da-a6ff-3e81b71ec0aa.png)

### Garden
```
unset http_proxy

export PYTHONHTTPSVERIFY=0
export http_proxy="http://10.32.2.3:8080"

date -s "7 NOV 2022 13:30:00"
speedtest
```
![Screenshot from 2022-11-07 20-00-01](https://user-images.githubusercontent.com/60770478/201641949-4d894288-a46a-440d-a0e4-d2103806a5ff.png)
