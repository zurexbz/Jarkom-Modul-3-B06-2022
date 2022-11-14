# Jarkom-Modul-1-B06-2022

### Kelompok 10 Kelas B

| **No** | **NRP** | **Nama** | 
| ------------- | ------------- | --------- |
| 1 | 5025201100 | Hansen Idden | 
| 2 | 5025201109 | Mohammad Firman Fardiansyah |
| 3 | 5025201167 | William Zefanya Maranatha |

## Settings
![image](https://user-images.githubusercontent.com/62301187/201665167-c4e1e4c1-d5e5-4d7c-838d-cc7e0538c667.png)
Konfigurasi tiap node

#### Ostania
```
# DHCP config for eth0
auto eth0
iface eth0 inet dhcp

# Static config for eth1
auto eth1
iface eth1 inet static
	address 10.6.1.1
	netmask 255.255.255.0

# Static config for eth2
auto eth2
iface eth2 inet static
	address 10.6.2.1
	netmask 255.255.255.0

# Static config for eth3
auto eth3
iface eth3 inet static
	address 10.6.3.1
	netmask 255.255.255.0
```

#### SSS
```
auto eth0
iface eth0 inet dhcp
```

#### Garden
```
auto eth0
iface eth0 inet dhcp
```

#### WISE
```
auto eth0
iface eth0 inet static
	address 10.6.2.2
	netmask 255.255.255.0
	gateway 10.6.2.1
```

#### Berlint
```
auto eth0
iface eth0 inet static
	address 10.6.2.3
	netmask 255.255.255.0
	gateway 10.6.2.1
```

#### WESTALIS
```
auto eth0
iface eth0 inet static
	address 10.6.2.4
	netmask 255.255.255.0
	gateway 10.6.2.1
```

#### Eden
```
auto eth0
iface eth0 inet dhcp
hwaddress ether 72:31:2a:53:2e:00
```

#### NewstonCastle
```
auto eth0
iface eth0 inet dhcp
```

#### KemonoPark
```
auto eth0
iface eth0 inet dhcp
```

## Soal 1
### Loid bersama Franky berencana membuat peta tersebut dengan kriteria WISE sebagai DNS Server, Westalis sebagai DHCP Server, Berlint sebagai Proxy Server

#### Ostania 
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.6.0.0/16
```

#### Wise
DNS Server
```
apt-get update
apt-get install bind9 -y
```

#### Westalis
DHCP Server
```
apt-get update
apt-get install isc-dhcp-server -y
```

#### Berlint
Proxy server
```
apt-get update
apt-get install squid -y
service squid start
```

## Soal 2
### Ostania sebagai DHCP Relay

#### Ostania
```
apt-get update
apt-get install isc-dhcp-relay -y
```
Lalu edit pada file ```/etc/default/isc-dhcp-relay``` untuk melakukan forward DHCP server dari IP 10.6.2.4 yaitu IP Westalis dan sesuai topologi, maka dilakukan forward pada ```eth1 eth2 eth3```. Pada file dapat ditambahkan
```
SERVER = "10.6.2.4"
INTERFACES = "eth1 eth2 eth3"
OPTIONS = ""
```

## Soal 3
### Loid dan Franky menyusun peta tersebut dengan hati-hati dan teliti.
### Ada beberapa kriteria yang ingin dibuat oleh Loid dan Franky, yaitu:
### A. Semua client yang ada HARUS menggunakan konfigurasi IP dari DHCP Server.
### B. Client yang melalui Switch1 mendapatkan range IP dari 10.6.1.50 - 10.6.1.88 dan 10.6.1.120 - 10.6.1.155

### A
Karena semua client harus menggunakan konfigurasi IP dari DHCP Server, maka tiap node client ditambahkan
```
auto eth0
iface eth0 inet dhcp
```
Node client nya yaitu:
- SSS
- Garden
- NewstonCastle
- Eden
- KemonoPark

### B
Maka kita dapat mengubah pada Westalis untuk menambahkan range IP
#### Switch 3
```
subnet 10.6.2.0 netmask 255.255.255.0{
    option routers 10.6.2.1;
}
```

#### Switch 1
```
subnet 10.6.1.0 netmask 255.255.255.0 {
    range 10.6.1.50 10.6.1.88;
    range 10.6.1.120 10.6.1.155;
    ...
}
```
pada file ```/etc/dhcp/dhcpd.conf```

## Soal 4
### c. Client yang melalui Switch3 mendapatkan range IP dari 10.6.3.10 - 10.6.3.30 dan 10.6.3.60 - 10.6.3.85
Sama seperti Nomor 3 menambahkan
#### Switch 2
```
subnet 10.6.3.0 netmask 255.255.255.0 {
        range 10.6.3.10 10.6.3.30;
        range 10.6.3.60 10.6.3.85;
        ...
}
```
pada file ```/etc/dhcp/dhcpd.conf```

## Soal 5
### d. Client mendapatkan DNS dari WISE dan client dapat terhubung dengan internet melalui DNS tersebut.
Untuk dapat mendapatkan DNS dari WISE dan terhubung dengan internet maka ditambahkan pada Switch 1 dan Switch 3
```
subnet 10.6.1.0 netmask 255.255.255.0 {
    ...
    option domain-name-servers 10.6.2.2;
    ...
}

subnet 10.6.3.0 netmask 255.255.255.0 {
    ...
    option domain-name-servers 10.6.2.2;
    ...
}
```
pada file ```/etc/dhcp/dhcpd.conf```

## Soal 6
### Lama waktu DHCP server meminjamkan alamat IP kepada Client yang melalui Switch1 selama 5 menit sedangkan pada client yang melalui Switch3 selama 10 menit. Dengan waktu maksimal yang dialokasikan untuk peminjaman alamat IP selama 115 menit.

Untuk membatasi lama waktu maka pada switch 1 dan switch 3 ditambahkan

```
subnet 10.6.1.0 netmask 255.255.255.0 {
    ...
    default-lease-time 300;
    max-lease-time 6900;
}

subnet 10.6.3.0 netmask 255.255.255.0 {
    ...
    default-lease-time 600;
    max-lease-time 6900;
}
```
pada file ```/etc/dhcp/dhcpd.conf```

## Soal 7
### Loid dan Franky berencana menjadikan Eden sebagai server untuk pertukaran informasi dengan alamat IP yang tetap dengan IP 10.6.3.13

Karena ingin menjadikan Eden sebagai server pada file ```/etc/dhcp/dhcpd.conf``` ditambahkan settingan
```
host Eden {
        hardware ethernet 72:31:2a:53:2e:00;
        fixed-address 10.6.3.13;
}
```
Lalu tambahkan hwaddress tersebut pada konfigurasi Eden

## Konfigurasi lengkap DHCP
![image](https://user-images.githubusercontent.com/62301187/201671101-4cd2a8f7-7eb8-4086-b269-ec6b16b57555.png)
![image](https://user-images.githubusercontent.com/62301187/201670845-77ddba51-e56e-49f1-bb1f-a1213444fb8c.png)

# Soal Proxy
### SSS, Garden, dan Eden digunakan sebagai client Proxy agar pertukaran informasi dapat terjamin keamanannya, juga untuk mencegah kebocoran data. Pada Proxy Server di Berlint, Loid berencana untuk mengatur bagaimana Client dapat mengakses internet. Artinya setiap client harus menggunakan Berlint sebagai HTTP & HTTPS proxy. Adapun kriteria pengaturannya adalah sebagai berikut:
Setup proxy menggunakan port 8080 karena pada soal tidak ada spesifik portnya
```
http_port 8080
visible_hostname Berlint
http_access allow all
```
Setelah itu restart node pada switch 1 dan switch 3, kemudian gunakan script berikut pada Eden, Garden, dan SSS
```
export http_proxy="http://10.6.2.3:8080"
env | grep -i proxy
```

## Soal 8
### a. Client hanya dapat mengakses internet diluar (selain) hari & jam kerja (senin-jumat 08.00 - 17.00) dan hari libur (dapat mengakses 24 jam penuh)
Karena pengaksesan internet dilarang pada hari dan jam kerja saja, maka bisa kita tambahkan script berikut pada Berlint
```
acl available_working time M T W H F 08:00-17:00
```
Setelah itu, set available_working agar tidak diberikan akses internet
```
http_access deny available_working
```
## Soal 9
### b. Adapun pada hari dan jam kerja sesuai nomor (1), client hanya dapat mengakses domain loid-work.com dan franky-work.com (IP tujuan domain dibebaskan)
Domain yang dapat diakses diiniasi dan dimasukkan ke dalam `/etc/squid/available-sites.acl`
```
echo -e '.loid-work.com
.franky-work.com
' > /etc/squid/available-sites.acl

acl sites_available_working dstdomain "/etc/squid/available-sites.acl"
```
Kemudian berikan akses ketika client mengakses pada hari dan jam kerja, maka hanya bisa mengakses domain tersebut dan ketika di luar waktu itu maka domain lain dapat diakses
```
http_access allow sites_available_working available_working
http_access deny sites_available_working
```

## Soal 10
### c. Saat akses internet dibuka, client dilarang untuk mengakses web tanpa HTTPS. (Contoh web HTTP: http://example.com)
Set agar web dengan http diblock dan hanya perbolehkan web dengan https menggunakan script berikut:
```
acl block_access_http url_regex -i 'http://'
acl access_ https url_regex -i 'https://'

http_access deny available_working access_https
http_access deny block_access_http
```
Namun, tetap ingat bahwa meskipun web tersebut https tetapi diakses pada jam kerja, maka tetap tidak bisa diakses kecuali domain pada nomor 9

## Soal 11
### d. Agar menghemat penggunaan, akses internet dibatasi dengan kecepatan maksimum 128 Kbps pada setiap host (Kbps = kilobit per second; lakukan pengecekan pada tiap host, ketika 2 host akses internet pada saat bersamaan, keduanya mendapatkan speed maksimal yaitu 128 Kbps)

## Soal 12
### e. Setelah diterapkan, ternyata peraturan nomor (4) mengganggu produktifitas saat hari kerja, dengan demikian pembatasan kecepatan hanya diberlakukan untuk pengaksesan internet pada hari libur

