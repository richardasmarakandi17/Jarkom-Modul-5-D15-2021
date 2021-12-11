# Jarkom-Modul-5-D15-2021

## Anggota Kelompok D15
## Richard Asmarakandi (05111940000017)
## Nurul Izzatil Ulum (05111940000058)


**Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Luffy dibawah ini:
Keterangan : Doriki adalah DNS Server
Jipangu adalah DHCP Server
Maingate dan Jorge adalah Web Server
Jumlah Host pada Blueno adalah 100 host
Jumlah Host pada Cipher adalah 700 host
Jumlah Host pada Elena adalah 300 host
Jumlah Host pada Fukurou adalah 200 host

**(B) Karena kalian telah belajar subnetting dan routing, Luffy ingin meminta kalian untuk
membuat topologi tersebut menggunakan teknik CIDR atau VLSM. setelah melakukan
subnetting,
(C) Kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut
dapat terhubung.
(D) Tugas berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan
Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat
bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.**

Untuk subnetting menggunakan teknik `VSLM`. pertama ditentukan dlu subnet subnetnya.
![SUBNETTING-1](https://user-images.githubusercontent.com/76694068/145677698-12d41ddf-3d73-4b6c-85f3-2d70e05d68f5.jpg)

Setelah menentukan subnetting, maka ditentukan berapa jumlah host untuk masing masing subnet dan menentukan lenghtnya.
![image](https://user-images.githubusercontent.com/76694068/145678278-22d44a03-7b7e-4570-944b-55409e75c075.png)

Setelah ketemu berapa lenght maxnya, maka gunakan tree untuk mendapatkan ip-ip yang diinginkan. cara menghitung treenya bisa dilihat dari wild card tiap lenght branch, dan dimodifikasi. untuk lebih lengkapnay bisa dilihat modul sbelumnya
![Copy of PohFak-Page-2 drawio](https://user-images.githubusercontent.com/76694068/145677953-1249466e-e45c-49a3-9089-c0e5f3e29bcd.png)

Setelah mendapatkan ip yang diinginkan, maka didaftar apap saja netmask, broadcast dan network ID.
![image](https://user-images.githubusercontent.com/76694068/145678290-c1f49ad8-91d6-4e04-b006-2041c1d53570.png)

Jika telah mendapatkan seluruh ip yang dibutuhkan, maka akan dilakukan routing dan setting ip di masing masing server.
```
BLUENO
eth0 : 192.199.7.1 / 255.255.255.128 / A1

ELENA
eth0 : 192.199.4.1 / 255.255.254.0 /A2

JORGE
eth0 : 192.199.7.129 / A3

MAINGATE
eth0 : 192.199.7.130 / A3

GUANHAO
eth0 : 192.199.7.145 / 255.255.255.252 / A4
eth1 : 192.199.4.2 / 255.255.254.0 / A2
eth2 : 192.199.7.131 / 255.255.255.248 / A3
eth3 : 192.199.6.1 / 255.255.255.0 / A7

FOOSHA
eth0 : dhcp
eth1 : 192.199.7.149 / 255.255.255.252 / A5
eth2 : 192.199.7.146 / 255.255.255.252 / A4

WATER7
eth0 : 192.199.7.149 / 255.255.255.252 / A5
eth1 : 192.199.7.2 / 255.255.255.128 / A1
eth2 : 192.199.7.137 / 255.255.255.248 / A6
eth3 : 192.199.0.1 / 255.255.252.0 / A8

JIPANGU
eth0 : 192.199.7.138 / 255.255.255.248 / A6

DORIKI
eth0 : 192.199.7.139 / 255.255.255.248 / A6

FUKUROU
eth0 : 192.199.6.2 / 255.255.255.0 / A7

CIPHER
eth0 : 192.199.0.2 / 255.255.252.0 / A8
```

Untuk routing, karean server yang lain sudah langsung terhubung dengan WATER7 dan GUANHAO, maka tidka perlu routing, yang perlu diroutig hanya di FOOSHA saja.
```
#############################ROUTING##############################
FOOSHA
route add -net 192.199.4.0 netmask 255.255.254.0 gw 192.199.7.145
route add -net 192.199.7.128 netmask 255.255.255.248 gw 192.199.7.145
route add -net 192.199.6.0 netmask 255.255.255.0 gw 192.199.7.145
route add -net 192.199.7.0 netmask 255.255.255.128 gw 192.199.7.150
route add -net 192.199.7.136 netmask 255.255.255.248 gw 192.199.7.150
route add -net 192.199.0.0 netmask 255.255.252.0 gw 192.199.7.150
```


### 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.
### 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan. 1.0 -> 1.1: Revisi Soal No. 2 dari “yang memiliki ip DHCP” menjadi “yang merupakan DHCP Server”
### 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop. Kemudian kalian diminta untuk membatasi akses ke Doriki yang berasal dari subnet Blueno, Cipher, Elena dan Fukuro dengan beraturan sebagai berikut
### 4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.
### 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya. Selain itu di reject
### 6. Karena kita memiliki 2 Web Server, Luffy ingin Guanhao disetting sehingga setiap request dari client yang mengakses DNS Server akan didistribusikan secara bergantian pada Jorge dan Maingate Luffy berterima kasih pada kalian karena telah membantunya. Luffy juga mengingatkan agar semua aturan iptables harus disimpan pada sistem atau paling tidak kalian menyediakan script sebagai backup.
