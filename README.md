# Jarkom-Modul-5-A03-2022

Anggota:
-   5025201164 - Fayyadh Hafizh
-   5025201013 - Adifa Widyadhani Chanda D
-   5025201140 - Ezekiel Mashal Wicaksono

## Soal Shift Modul 5

Setelah kalian mempelajari semua modul yang telah diberikan, Loid ingin meminta bantuan untuk terakhir kalinya kepada kalian. Dan kalian dengan senang hati mau membantu Loid.

-   Tugas pertama kalian yaitu membuat topologi jaringan sesuai dengan rancangan yang diberikan Loid seperti di soal
    
    **Keterangan:**
    - Eden adalah DNS Server
    - WISE adalah DHCP Server
	- Garden dan SSS adalah Web Server
	- Jumlah Host pada Forger adalah 62 host
	- Jumlah Host pada Desmond adalah 700 host
	- Jumlah Host pada Blackbell adalah 255 host
	- Jumlah Host pada Briar adalah 200 host
    
-   Untuk menjaga perdamaian dunia, Loid ingin meminta kalian untuk membuat topologi tersebut menggunakan teknik CIDR atau VLSM setelah melakukan subnetting.
-   Anya, putri pertama Loid, juga berpesan kepada anda agar melakukan Routing agar setiap perangkat pada jaringan tersebut dapat terhubung.
-   Tugas berikutnya adalah memberikan ip pada subnet Forger, Desmond, Blackbell, dan Briar secara dinamis menggunakan bantuan DHCP server. Kemudian kalian ingat bahwa kalian harus setting DHCP Relay pada router yang menghubungkannya.

## Soal 1

### Agar topologi yang kalian buat dapat mengakses keluar, kalian diminta untuk mengkonfigurasi Strix menggunakan iptables, tetapi Loid tidak ingin menggunakan MASQUERADE.

### Jawaban:

- Pertama membagi *subnet* (*subnetting*) terhadap topologi. Di sini kelompok kami menggunakan teknik **VLSM** (*Variable Length Subnet Masking*) dalam pembagiannya.
 ![](https://cdn.discordapp.com/attachments/949602435100467230/1051076645232066560/Screen_Shot_2022-12-10_at_17.00.42.png)

- Penentuan jumlah alamat IP yang dibutuhkan tiap *subnet* dan ***labelling netmask*** berdasarkan jumlah IP. Berdasarkan hasil perhitungan, maka menggunakan *length* **/21** untuk *netmask*-nya.

  ![](https://cdn.discordapp.com/attachments/949602435100467230/1051078573039042560/Screen_Shot_2022-12-10_at_17.09.45.png)

- Pembuatan ***tree subnet*** untuk nantinya membagi IP berdasarkan **NID** dan ***netmask***-nya.

  ![](https://cdn.discordapp.com/attachments/949602435100467230/1051079230248730694/Screen_Shot_2022-12-10_at_17.12.50.png)

- Pembagian IP dan *netmask* dengan tabel berdasarkan ***tree subnet*** yang sudah dibuat.

 

Kemudian, pada **GNS3** membuat topologi sesuai permintaan soal. *Setting network* masing-masing *node* dengan fitur `Edit network configuration` yang ada di menu `Configure` sesuai pembagian IP menggunanakan VLSM sebelumnya. Berikut konfigurasinya:

- Strix (Router)
  ```
    auto lo
    iface lo inet loopback
  
    auto eth0
    iface eth0 inet static
        address 192.168.122.50
        netmask 255.255.255.0
        gateway 192.168.122.1
        
    auto eth1
    iface eth1 inet static
	    address 192.170.0.5
	    netmask 255.255.255.252

    auto eth2
    iface eth2 inet static
	    address 192.170.0.2
	    netmask 255.255.255.252
  ```
  
- Wistalis (Router & DHCP Relay)
  ```
    auto lo
    iface lo inet loopback

    auto eth0
    iface eth0 inet static
        address 192.170.0.1
        netmask 255.255.255.252
        gateway 192.170.0.2
    
    auto eth1
    iface eth1 inet static
    	address 192.170.4.1
    	netmask 255.255.252.0
    
    auto eth2
    iface eth2 inet static
    	address 192.170.0.129
    	netmask 255.255.255.128
    
    auto eth3
    iface eth3 inet static
    	address 192.170.0.17
    	netmask 255.255.255.248
  ```
  
- Ostania (Router & DHCP Relay)
  ```
    auto lo
    iface lo inet loopback
    
    auto eth0
    iface eth0 inet static
        address 192.170.0.6
        netmask 255.255.255.252
        gateway 192.170.0.5
    
    auto eth1
    iface eth1 inet static
    	address 192.170.2.1
    	netmask 255.255.254.0
    
    auto eth2
    iface eth2 inet static
    	address 192.170.1.1
    	netmask 255.255.255.0
    
    auto eth3
    iface eth3 inet static
    	address 192.170.0.25
    	netmask 255.255.255.248

  ```
  
- Eden (DNS Server)
  ```
    auto eth0
    iface eth0 inet static
        address 192.170.0.19
        netmask 255.255.255.248
        gateway 192.170.0.17
  ```
  
- Wise (DHCP Server)
  ```
    auto eth0
    iface eth0 inet static
        address 192.170.0.18
        netmask 255.255.255.248
        gateway 192.170.0.17
  ```
  
- Garden (Web Server)
  ```
    auto eth0
    iface eth0 inet static
        address 192.170.0.26
        netmask 255.255.255.248
        gateway 192.170.0.25
  ```
  
- SSS (Web Server)
  ```
    auto eth0
    iface eth0 inet static
        address 192.170.0.27
        netmask 255.255.255.248
        gateway 192.170.0.25
  ```
  
- Forger (Client)
  ```
  auto eth0
  iface eth0 inet dhcp
  ```
  
- Desmond (Client)
  ```
  auto eth0
  iface eth0 inet dhcp
  ```
  
- Blackbell (Client)
  ```
  auto eth0
  iface eth0 inet dhcp
  ```
  
- Briar (Client)
  ```
  auto eth0
  iface eth0 inet dhcp
  ```

### Strix

Setelah selesai mengonfigurasi *node*, jalankan *command* berikut pada *router* `Strix` untuk pengaturan lalu lintas komputer.
```
tables -t nat -A POSTROUTING -o eth0 -s 192.170.0.0/21 -j SNAT --to-source 192.168.122.50
```
Lalu, lakukan *routing* pada **Strix** seperti berikut:
```
#Node Bawah
route add -net 192.170.0.16 netmask 255.255.255.248 gw 192.170.0.1
route add -net 192.170.0.128 netmask 255.255.255.128 gw 192.170.0.1
route add -net 192.170.4.0 netmask 255.255.252.0 gw 192.170.0.1
#Node Kanan
route add -net 192.170.1.0 netmask 255.255.255.0 gw 192.170.0.6
route add -net 192.170.2.0 netmask 255.255.254.0 gw 192.170.0.6
route add -net 192.170.0.24 netmask 255.255.255.248 gw 192.170.0.6
```

*Testing* pada **Strix** dengan `ping` ke [**google.com**](https://www.google.com).
![](https://cdn.discordapp.com/attachments/949602435100467230/1051092656295514172/Screen_Shot_2022-12-10_at_18.06.11.png)

### Semua node (kecuali Strix)

Agar *node*-*node* lainnya dapat mengakses internet, jalankan *command* berikut dan gunakan IP DNS dari `Strix`.
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
```

### Wistalis dan Ostania

Lakukan *setting* **DHCP Relay** dengan *install* terlebih dahulu. *Command* yang dijalankan adalah sebagai berikut.
```
apt-get update
apt-get install isc-dhcp-relay -y
```

Buka *file* **/etc/default/isc-dhcp-relay** dan edit seperti konfigurasi berikut.

  ![](https://cdn.discordapp.com/attachments/949602435100467230/1051090785061322752/Screen_Shot_2022-12-10_at_17.58.20.png)

*Restart* **DHCP Relay**.
```
service isc-dhcp-relay restart
```

Untuk memastikan apakah DHCP Relay berjalan, dapat menggunakan *command* berikut.
```
service isc-dhcp-relay status
```

### Wise

Lakukan *setting* **DHCP Server** dengan *install* terlebih dahulu. *Command* yang dijalankan adalah sebagai berikut.
```
apt-get update
apt-get install isc-dhcp-server -y
```

Buka *file* **/etc/default/isc-dhcp-server** dan edit bagian paling bawah seperti konfigurasi berikut.

![](https://cdn.discordapp.com/attachments/949602435100467230/1051090785405251614/Screen_Shot_2022-12-10_at_17.54.46.png)

Buka *file* lagi, yaitu **/etc/dhcp/dhcpd.conf** dan edit seperti konfigurasi berikut.
```
#A2
subnet 192.170.0.128 netmask 255.255.255.128 {
    range 192.170.0.130 192.170.0.254;
    option routers 192.170.0.129;
    option broadcast-address 192.170.0.255;
    option domain-name-servers 192.170.0.19;

    default-lease-time 360;
    max-lease-time 7200;
}

#A3
subnet 192.170.4.0 netmask 255.255.252.0 {
    range 192.170.4.2 192.170.7.254;
    option routers 192.170.4.1;
    option broadcast-address 192.170.7.255;
    option domain-name-servers 192.170.0.19;
    default-lease-time 360;
    max-lease-time 7200;
}

#A6
subnet 192.170.1.0 netmask 255.255.255.0 {
    range 192.170.1.2 192.170.1.254;
    option routers 192.170.1.1;
    option broadcast-address 192.170.1.255;
    option domain-name-servers 192.170.0.19;
    default-lease-time 360;
    max-lease-time 7200;
}


#A7
subnet 192.170.2.0 netmask 255.255.254.0 {
    range 192.170.2.2 192.170.3.254;
    option routers 192.170.2.1;
    option broadcast-address 192.170.3.255;
    option domain-name-servers 192.170.0.19;
    default-lease-time 360;
}

#A1
subnet 192.170.0.16 netmask 255.255.255.248 {
        option routers 192.170.0.17;
}
```

*Restart* **DHCP Server**.
```
service isc-dhcp-server restart
```
Untuk memastikan apakah DHCP Server berjalan, dapat menggunakan *command* berikut.
```
service isc-dhcp-server status
```

### Semua *Client*

Sehabis mengonfigurasi **DHCP Server** dan **DHCP Relay**, *restart* semua *node client* agar *service* DHCP dapat berjalan. 

## Soal 2

### Kalian diminta untuk melakukan drop semua TCP dan UDP dari luar Topologi kalian pada server yang merupakan DHCP Server demi menjaga keamanan. 

### Jawaban:

### Strix

Masukkan *command* berikut pada *node* **Strix**:
```
iptables -A FORWARD -p tcp --dport 80 -d 192.170.0.16/29 -i eth0 -j DROP
```
- `-A FORWARD` digunakan untuk mendefinisikan *chain* **FORWARD**.
- `-p tcp` digunakan untuk mendefinisikan protokol **TCP**.
-  `80` digunakan untuk mendefinisikan *port* **80**.
-  `d 10.3.7.128/29` digunakan untuk mendefinisikan alamat tujuan dari paket (dalam hal ini DHCP Server dan DNS Server) yang berada pada **192.170.0.16/29**.
-  `i eth0` digunakan untuk memasukkan paket dari **eth0 Strix**.
-  `-j DROP` digunakan untuk men-*drop* paket.

### Strix atau Wise & Eden

Kita bisa melakukan *testing* dengan 2 cara, yaitu dengan *ping* website yang berprotokol HTTP pada **Wise**/**Eden** ataupun dengan `nmap -p 80 192.170.0.16` pada **Strix** (dalam hal ini perlu meng-*install* **netcat** pada *node* **Wise**/**Eden**).

- Menggunakan *ping*

  ![](https://cdn.discordapp.com/attachments/949602435100467230/1051120277158903929/Screen_Shot_2022-12-10_at_19.53.56.png)
  
- Menggunakan `nmap` (sebelumnya pada **Wise**/**Eden** telah menjalankan *command* `nc -l -p 80`)
    ![](https://cdn.discordapp.com/attachments/949602435100467230/1051120276731080734/Screen_Shot_2022-12-10_at_19.55.44.png)

## Soal 3

### Loid meminta kalian untuk membatasi DHCP dan DNS Server hanya boleh menerima maksimal 2 koneksi ICMP secara bersamaan menggunakan iptables, selebihnya didrop.

### Jawaban:
Untuk melakukan pembatasan jumlah icmp/ping pada DHCP dan DNS Server, maka kita akan menggunakan 2 command ```iptables``` seperti dibawah ini.

Pada Eden dan WISE
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 2 --connlimit-mask 0 -j DROP
```
![image](https://user-images.githubusercontent.com/72655301/206858826-f261ce3d-191c-428d-85bb-f3d827550a62.png)

## Soal 4

### Akses menuju Web Server hanya diperbolehkan disaat jam kerja yaitu Senin sampai Jumat pada pukul 07.00 - 16.00.

### Jawaban:
Dengan menggunakan command ```iptables```, kita akan menambah rules pada Garden dan SSS dengan keyword time untuk spesifikasi waktu seperti dibawah ini.

```
iptables -A INPUT -d 192.170.0.24/29 -m time --timestart 07.00 --timestop 16.00 --weekdays Mon,Tue,Wed,Thu,Fri -j ACCEPT
iptables -A INPUT -d 192.170.0.24/29 -j DROP
```

![image](https://user-images.githubusercontent.com/72655301/206858953-05185dbb-c019-49de-a39d-6e53092780cf.png)

## Soal 5

### Karena kita memiliki 2 Web Server, Loid ingin Ostania diatur sehingga setiap request dari client yang mengakses Garden dengan port 80 akan didistribusikan secara bergantian pada SSS dan Garden secara berurutan dan request dari client yang mengakses SSS dengan port 443 akan didistribusikan secara bergantian pada Garden dan SSS secara berurutan.

### Jawaban:
Untuk melakukan balancing, kita akan menggunakan beberapa command ```iptables``` sebagai berikut

```
iptables -A PREROUTING -t nat -p tcp -d 192.170.0.26 --dport 80 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to destination 192.170.0.26
iptables -A PREROUTING -t nat -p tcp -d 192.170.0.26 --dport 80 -j DNAT --to-destination 192.170.0.27

iptables -A PREROUTING -t nat -p tcp -d 192.170.0.26 --dport 443 -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.170.0.26
iptables -A PREROUTING -t nat -p tcp -d 192.170.0.26 --dport 443 -j DNAT --to-destination 192.170.0.27

iptables -A POSTROUTING -t nat -p tcp -d 192.170.0.26 -j SNAT --to-source 192.170.255.255
iptables -A POSTROUTING -t nat -p tcp -d 192.170.0.27 -j SNAT --to-source 192.170.255.255
```

## Soal 6

### Karena Loid ingin tau paket apa saja yang di-drop, maka di setiap node server dan router ditambahkan logging paket yang di-drop dengan standard syslog level.

### Jawaban: -

## Kendala
- Susah untuk mengerjakan semua modul yang digabung menjadi satu
- Untuk soal nomor 6 bingung cara pengerjaannya
