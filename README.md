# Laporan Praktikum Modul 3

## Anggota  Kelompok D15 :

## Richard Asmarakandi (05111940000017)

## Nurul Izzatil Ulum (05111940000058)

Pertama untuk menyambungkan node2 lainnya ke internet, isikan `iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.192.0.0/16` pada roter "Foosha"
Isikan `echo nameserver 192.168.122.1 > /etc/resolv.conf` pada server2 lainnya agar nameservernya terganti dan bisa menerima internet. dan atur konfigurasi nodenya sesuai dengan modul sebelumnya.


## SOAL
### A. Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Luffy.
![Topologi](https://cdn.discordapp.com/attachments/804405775988555776/919234886013947924/unknown.png)

### B. Karena kalian telah belajar subnetting dan routing, Luffy ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM.

* Untuk subnetting menggunakan teknik `VSLM`. pertama ditentukan dlu subnet subnetnya.
![SUBNETTING-1](https://user-images.githubusercontent.com/76694068/145677698-12d41ddf-3d73-4b6c-85f3-2d70e05d68f5.jpg)

* Setelah menentukan subnetting, maka ditentukan berapa jumlah host untuk masing masing subnet dan menentukan lenghtnya.
![image](https://user-images.githubusercontent.com/76694068/145677867-d9d0c991-597f-4580-a0d5-00ea73e2ac70.png)

* Setelah ketemu berapa lenght maxnya, maka gunakan tree untuk mendapatkan ip-ip yang diinginkan. cara menghitung treenya bisa dilihat dari wild card tiap lenght branch, dan dimodifikasi. untuk lebih lengkapnay bisa dilihat modul sbelumnya
![Copy of PohFak-Page-2 drawio](https://user-images.githubusercontent.com/76694068/145677953-1249466e-e45c-49a3-9089-c0e5f3e29bcd.png)

* Setelah mendapatkan ip yang diinginkan, maka didaftar apa saja netmask, broadcast dan network ID.
![image](https://user-images.githubusercontent.com/76694068/145677945-60bd8808-cb7b-4554-b822-cdd76e24a6b2.png)

* Jika telah mendapatkan seluruh ip yang dibutuhkan, maka akan dilakukan routing dan setting ip di masing masing server.
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

### C. Kalian juga diharuskan melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.

* Karena topologinya cukup sederhana, sesuai gambar yang perlu dilakukan routing hanyalah router Foosha, yaitu pada subnet A1, A8, A6, A2, A3, A7. Router Water7 secara langsung telah terhubung dengan subnet A1, A6 dan A8. Router Guanhao juga secara langsung telah terhubung dengan A2, A3, A7.

![Topologi](https://cdn.discordapp.com/attachments/804405775988555776/919215058175791144/unknown.png)

* Maka untuk pengaturan router pada Foosha kita tambahkan command-command berikut pada Foosha :
```
route add -net 192.199.4.0 netmask 255.255.254.0 gw 192.199.7.145
route add -net 192.199.7.128 netmask 255.255.255.248 gw 192.199.7.145
route add -net 192.199.6.0 netmask 255.255.255.0 gw 192.199.7.145
route add -net 192.199.7.0 netmask 255.255.255.128 gw 192.199.7.150
route add -net 192.199.7.136 netmask 255.255.255.248 gw 192.199.7.150
route add -net 192.199.0.0 netmask 255.255.252.0 gw 192.199.7.150
```

### D. Tugas berikutnya adalah memberikan ip pada subnet Blueno, Cipher, Fukurou, dan Elena secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

* Pertama-tama kita atur network configuration untuk Blueno, Cipher, Fukurou dan Elena. gunakan konfigurasi seperti berikut pada setiap client (sesuaikan eth0 dengan interface yang terhubung pada topologi):
```
auto eth0
iface eth0 inet dhcp
```
![DHCP pada client](https://cdn.discordapp.com/attachments/804405775988555776/919217651023216670/unknown.png)

* Setelah itu kita install dhcp server pada jipangu dengan command `apt-get install isc-dhcp-server`. setelah itu tambahkan konfigurasi pada `/etc/dhcp/dhcpd.conf` di line paling bawah dengan :
```
subnet 192.199.7.136 netmask 255.255.255.248 {
}

subnet 192.199.7.0 netmask 255.255.255.128 {
    range 192.199.7.1 192.199.7.126;
    option routers 192.199.7.2;
    option broadcast-address 192.199.7.127;
    option domain-name-servers 192.199.7.139;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 192.199.0.0 netmask 255.255.252.0 {
    range 192.199.0.1 192.199.3.254;
    option routers 192.199.0.1;
    option broadcast-address 192.199.7.127;
    option domain-name-servers 192.199.7.139;
    default-lease-time 600;
    max-lease-time 7200;
}
subnet 192.199.4.0 netmask 255.255.254.0 {
    range 192.199.4.1 192.199.5.254;
    option routers 192.199.4.2;
    option broadcast-address 192.199.5.255;
    option domain-name-servers 192.199.7.139;
    default-lease-time 600;
    max-lease-time 7200;
}

subnet 192.199.6.0 netmask 255.255.255.0 {
    range 192.199.6.1 192.199.6.254;
    option routers 192.199.6.1;
    option broadcast-address 192.199.6.255;
    option domain-name-servers 192.199.7.139;
    default-lease-time 600;
    max-lease-time 7200;
}
```

![Setting dhcpd.conf Jipangu](https://cdn.discordapp.com/attachments/804405775988555776/919219211102994502/unknown.png)

* Ubah juga setting pada `/etc/default/isc-dhcp-server` menjadi seperti berikut (Sesuaikan Interfaces dengan interface yang terhubung):

![Setting isc-dhcp-server Jipangu](https://cdn.discordapp.com/attachments/804405775988555776/919219745675444274/unknown.png)

* Jalankan dhcp server Setelah itu kita install dhcp relay untuk setiap router. Pastikan setting server request mengarah ke IP Jipangu dan interface sesuai dengan yang terhubung.

* Setelah kita jalankan dhcp relay ketiga router seharusnya ketika kita menyalakan salah satu client akan didapatkan IP sesuai dengan konfigurasi di DHCP server.

### 1. Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Foosha menggunakan iptables, tetapi Luffy tidak ingin menggunakan MASQUERADE.

* Untuk melakukannya kita dapat gunakan command seperti berikut pada router Foosha :
```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source [IP interface Foosha yang terhubung ke NAT]
```
* Apabila berhasil, seharusnya seluruh topologi dapat mengakses internet luar.

![Ping Google](https://cdn.discordapp.com/attachments/804405775988555776/919221670386999326/unknown.png)

### 2. Kalian diminta untuk mendrop semua akses HTTP dari luar Topologi kalian pada server yang merupakan DHCP Server dan DNS Server demi menjaga keamanan.

* Untuk melakukannya kita tambahkan rules iptables di Jipangu dan Doriki selaku DNS Server dan DHCP Server dengan command berikut :
```
iptables -A INPUT -p tcp --dport 80 -j DROP
```
* Untuk mengeceknya kita coba gunakan command `nmap -p 80 [IP Doriki` dan `nmap -p 80 [IP Jipangu]`. Apabila nanti outputnya port 80 filtered maka berhasil.

![nmap ke Jipangu](https://cdn.discordapp.com/attachments/804405775988555776/919223979850481754/unknown.png)
![nmap ke Doriki](https://cdn.discordapp.com/attachments/804405775988555776/919224223183024168/unknown.png)

### 3. Karena kelompok kalian maksimal terdiri dari 3 orang. Luffy meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 3 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

* Untuk melakukannya kita tambahkan rules iptables di Jipangu dan Doriki selaku DNS Server dan DHCP Server dengan command berikut :
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
* Untuk mengeceknya kita coba ping Doriki dan Jipangu dengan 4 client. Apabila ketika ping ke-4 gagal tersambung berarti pengaturan telah berhasil

![ping 1](https://cdn.discordapp.com/attachments/804405775988555776/919225157548134400/unknown.png)
![ping 2](https://cdn.discordapp.com/attachments/804405775988555776/919225169900372028/unknown.png)
![ping 3](https://cdn.discordapp.com/attachments/804405775988555776/919225226737369138/unknown.png)
![ping 4](https://cdn.discordapp.com/attachments/804405775988555776/919226484823040000/unknown.png)

## Kemudian kalian diminta untuk membatasi akses ke Doriki yang berasal dari subnet Blueno, Cipher, Elena dan Fukuro dengan beraturan sebagai berikut. 

### 4. Akses dari subnet Blueno dan Cipher hanya diperbolehkan pada pukul 07.00 - 15.00 pada hari Senin sampai Kamis.

* Untuk melakukannya kita tambahkan rules iptables di Doriki dengan command berikut :
```
iptables -A INPUT -s 192.199.7.0/25 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 192.199.7.0/25 -j REJECT
iptables -A INPUT -s 192.199.0.0/22 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
iptables -A INPUT -s 192.199.0.0/22 -j REJECT
```
* Apabila ping berhasil dan gagal sesuai dengan perintah soal, maka pengaturan berhasil
  
![Ping Blueno to Doriki](https://cdn.discordapp.com/attachments/804405775988555776/919233136179699783/unknown.png)

### 5. Akses dari subnet Elena dan Fukuro hanya diperbolehkan pada pukul 15.01 hingga pukul 06.59 setiap harinya.

* Untuk melakukannya kita tambahkan rules iptables di Doriki dengan command berikut :
```
iptables -A INPUT -s 192.199.4.0/23 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 192.199.6.0/24 -m time --timestart 15:01 --timestop 06:59 -j ACCEPT
iptables -A INPUT -s 192.199.4.0/23 -j REJECT
iptables -A INPUT -s 192.199.6.0/24 -j REJECT
```
* Apabila ping berhasil dan gagal sesuai dengan perintah soal, maka pengaturan berhasil

![Ping Fukurou ke Doriki](https://cdn.discordapp.com/attachments/804405775988555776/919234509998800946/unknown.png)