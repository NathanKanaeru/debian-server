# Bahasa

# 

Halo kembali lagi dengan saya NathanKanaeru . Setelah tadi kalian sudah install [Debian](installdebian.md) di virtualbox , kini saya akan bagikan tutorial bagaimana cara install PowerDNS dan Admin panelnya . Simak tutorial selengkapnya!

## Login dengan SSH

di tutorial sebelumnya saya sudah kasih tau bagaimana cara install openssh kan? nah sekarang login ssh ke debian seperti sebelumnya. (skip aja kalo udah login)
1. Buka Command Prompt / PowerShell / Windows Terminal
2. Lalu jalankan perintah seperti ini :
   ```
   ssh debian@192.168.100.1
   ```
   ini sama kok sama yang sebelumnya jadi kalo tadi udah login kayak gini mending skip aja wkwk

## Install Dependency & Database

1. Switch ke root user
   `sudo su`
2. Update & upgrade package
   ```bash
   apt update -y && apt upgrade -y
   ```
3. Install mariadb
   ```bash
   apt install mariadb-server mariadb-client -y
   ```
   Kalo error coba kode dibawah (kalo yang install diatas ga eror skip aja bagian ini!)
   ```bash
   apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
   ```
4. Instalasi database
   ```
   mysql_secure_installation
   ```
   Bakalan muncul benerapa pertanyaan:
   `Enter current password for root`
    - pada bagian ini langsung enter saja
   `Set root password?`
    - klik Y lalu enter dan buat password kamu
   `Remove anonymous user?` Y
   `Disable root login remotely` Y
   `Remove test database and acces to it` Y
   `Reload privilege now` Y
5. Buat Database
   Pertama tama login dulu ke mariadb dengan perintah `mysql -u root -p` dan masukin passowrd yang kamu buat pas setup database tadi
   jika sudah masuk ke CLI mariadb:
   buat user baru:
   ```
   create user 'pdns@localhost' identified by 'passwordmu';
   ```
   buat database baru:
   ```
   create database pdnsdb;
   ```
   set permission database ke user
   ```
   grant all privileges on pdns.* to pdnsadmin@localhost identified by '123';
   ```
   flush privileges dan keluar dari mariadb
   ```
   flush privileges;
   exit;
   ```
