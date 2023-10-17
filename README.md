# Jarkom-Modul-2-F02-2023
Laporan resmi praktikum modul 2 jaringan komputer 2023

|NAMA|NRP|
|:--:|:-:|
|Moh Adib Syambudi|5025211017|

## Soal 1
#### Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada drive berikut 

Berikut adalah topologi yang kelompok kami gunakan 


Adapun konfigurasi untuk tiap node seperti berikut

Pandudewanata sebagai router

```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.222.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.222.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.222.3.1
	netmask 255.255.255.0
```

Nakula sebagai client

```
auto eth0
iface eth0 inet static
	address 192.222.1.2
	netmask 255.255.255.0
	gateway 192.222.1.1
```

Sadewa sebagai client

```
auto eth0
iface eth0 inet static
	address 192.222.1.3
	netmask 255.255.255.0
	gateway 192.222.1.1
```

Yudishtira sebagai DNS master

```
auto eth0
iface eth0 inet static
	address 192.222.2.2
	netmask 255.255.255.0
	gateway 192.222.2.1
```

Werkudara sebagai DNS slave

```
auto eth0
iface eth0 inet static
	address 192.222.2.3
	netmask 255.255.255.0
	gateway 192.222.2.1
```

Arjuna sebagai load balancer

```
auto eth0
iface eth0 inet static
	address 192.222.3.2
	netmask 255.255.255.0
	gateway 192.222.3.1
```

Wisanggeni sebagai web server

```
auto eth0
iface eth0 inet static
	address 192.222.3.3
	netmask 255.255.255.0
	gateway 192.222.3.1
```

Abimanyu sebagai web server

```
auto eth0
iface eth0 inet static
	address 192.222.3.4
	netmask 255.255.255.0
	gateway 192.222.3.1
```

Prabukusuma sebagai web server

```
auto eth0
iface eth0 inet static
	address 192.222.3.5
	netmask 255.255.255.0
	gateway 192.222.3.1
```
Kemudian untuk testing topologi sudah berfungsi dengan baik kita lakukan ping pada google.com

![google](google.com.png)

## Soal 2
#### Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

Proses pembuatan dari dns arjuna.f02.com dan alias www.arjuna.f02.com sebagai berikut

-Instalasi dari kebutuhan pembuatan dns yaitu package bind9 dengan command `apt-get install bind9 -y`
-Kemudian kita edit file `/etc/bind/named.conf.local` dengan isi seperti berikut:
```
zone "arjuna.f10.com" {
	type master;
	file "/etc/bind/praktikum2/arjuna.f10.com";
};
```
- Edit juga file konfigurasi untuk dnsnya dengan menyalin `file /etc/bind/db.local` kedalam `/etc/bind/praktikum2/arjuna.f02.com. Untuk dns menerjemahkan ip dari node load balancer arjuna yaitu 192.222.3.2 ubah file menjadi berikut:
```
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	arjuna.f02.com. root.arjuna.f02.com. (
			2022100601		; Serial
			604800		; Refresh
			86400			; Retry
			2419200		; Expire
			604800 )		; Negative Cache TTL
;
@	IN	NS		arjuna.f02.com.
@	IN	A		192.222.3.2		; IP Arjuna
www	IN	CNAME		arjuna.f02.com
```
- Terakhir jalankan command `service bind9 restart`
- Percobaan dilakukan di client dengan ping ke arah dns yang telah dibuat yaitu arjuna.f02.com

![arjuwww](pingarju.png)

![arjualias](wwwarjuabi.png)

## Soal 3
#### Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com.

Dengan langkah yang sama dengan sebelumnya kita hanya perlu mengganti konfigurasi denngan abimanyu.f02.com yang mengarah ke node abimanyu dengan ip 192.222.3.4. Testingnya menggunakan client dengan ping menuju abimanyu.f02.com. Tambahkan juga konfigurasi berikut kedalam file `/etc/bind/named.conf.local`

```
zone "abimanyu.f02.com" {
	type master;
	file "/etc/bind/praktikum2/abimanyu.f02.com";
};
```

![abi](pingabiarju.png)

![abiwww](wwwabim.png)

## Soal 4
#### Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

Subdomain dibuat dengan penambahan pada konfigurasi `etc/bind/praktikum2/abimanyu.f02.com` dengan menambahkan record berikut
```
parikesit	IN	A		192.222.3.4
```
Di uji dengan ping parikesit.abimanyu.f02.com di client

![pari](parikesit.png)

## Soal 5
#### Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

- Reverse domain dibuat dengan menambahkan konfigurasi pada `/etc/bind/named.conf.local` dengan isi seperti berikut:
```
zone \"3.226.192.in-addr.arpa\" {
    type master;
    file \"/etc/bind/jarkom/3.222.192.in-addr.arpa\";
};
```
- Kemudian salin file `etc/bind/db.local` ke `/etc/bind/praktikum2/3.222.192.in-addr.arpa` dengan ubah isinya menjadi berikut:
```
; BIND data file for local loopback interface
;
$TTL	604800
@	IN	SOA	abimanyu.f02.com. root.abimanyu.f02.com. (
			2023101101		; Serial
			604800		; Refresh
			86400			; Retry
			2419200		; Expire
			604800 )		; Negative Cache TTL
;
3.222.192.in-addr.arpa.	IN	NS	abimanyu.f02.com.
4				IN	PTR	abimanyu.f02.com.	; Byte ke-4 Abimanyu'

```
- Restart bind9 dan install dnsutils untuk pengujian menggunakan command `host`. Pengujian dilakukan di client dengan
```
host -t PTR 192.222.3.4
```

![rever](reverse.png)

## Soal 6
#### Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
