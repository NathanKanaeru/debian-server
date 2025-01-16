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
   
3. Update & upgrade package
   ```bash
   apt update -y && apt upgrade -y
   ```
   
4. Install mariadb
   ```bash
   apt install mariadb-server mariadb-client -y
   ```
   Kalo error coba kode dibawah (kalo yang install diatas ga eror skip aja bagian ini!)
   ```bash
   apt-key adv --fetch-keys 'https://mariadb.org/mariadb_release_signing_key.asc'
   ```
   
5. Instalasi database
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

6. Buat Database
   
   Pertama tama login dulu ke mariadb dengan perintah `mysql -u root -p` dan masukin passowrd yang kamu buat pas setup database tadi
   jika sudah masuk ke CLI mariadb:
   - buat user baru:
   ```
   create user 'pdns@localhost' identified by 'passwordmu';
   ```
   - buat database baru:
   ```
   create database pdnsdb;
   ```
   - set permission database ke user
   ```
   grant all privileges on pdns.* to pdnsadmin@localhost identified by '123';
   ```
   - flush privileges dan keluar dari mariadb
   ```
   flush privileges;
   exit;
   ```

7. Install dan Konfigurasi PowerDNS
   - Jalankan :
   ```
   apt install pdns-server pdns-backend-mysql -y
   ```
   - selanjutnya :
   ```
   mysql -u root -p -D pdnsdb < /usr/share/pdns-backend-mysql/schema/schema.mysql.sql
   ```
   - setup config :
   ```
   nano /etc/powerdns/pdns.d/pdns.local.gmysql.conf
   ```
     - pastekan ini di file tsb(ganti password sesuai dengan password mu tadi
       ```
       # MySQL Configuration
       # MySQL Configuration
       #
       # Launch gmysql backend
       launch+=gmysql

       # gmysql parameters
       gmysql-host=127.0.0.1
       gmysql-port=3306
       gmysql-dbname=pdnsdb
       gmysql-user=root
       gmysql-password=passwordkamu
       gmysql-dnssec=yes
       # gmysql-socket=
       ```
       setelah dipaste klik `CTRL + X` -> `Y` -> `Enter`
       lalu kasih akses dulu :
       ```
       chmod 640 /etc/powerdns/pdns.d/pdns.local.gmysql.conf
       ```
       
   - Restart PDNS
     ```
     systemctl stop pdns
     pdns_server --daemon=no --guardian=no --loglevel=9
     systemctl start pdns
     ```
     
8. Install admin panel
   - Install dependency :
     ```
     apt install -y wget apache2 gettext libapache2-mod-php php php-common php-curl php-dev php-gd php-pear php-imap php-mysql php-xmlrpc php-intl
     ```
   - Install ekstensi database :
     ```
     pear install DB
     ```
   - Restart Apache :
     ```
     systemctl restart apache2.service
     ```
   - Download & Ekstrak source code poweradmin :
     ```
     wget https://github.com/poweradmin/poweradmin/archive/refs/tags/v3.4.1.tar.gz
     tar xvzf v3.4.1.tar.gz
     ```
   - Pindahkan dan berikan permission :
     ```
     mv poweradmin-3.4.1 /var/www/html/poweradmin
     chown -R www-data:www-data /var/www/html/poweradmin/
     ```
