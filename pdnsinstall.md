# Bahasa

# Install PowerDNS dan PowerAdmin

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
   `Enter current password for root` pada bagian ini langsung enter saja
   `Set root password?` mlik Y lalu enter dan buat password kamu misal `password`
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

# Install dan Konfigurasi PowerDNS
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

# Install admin panel
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

# Akses PowerAdmin

Setelah kamu lakukan semua diatas seharusnya kamu bisa akses poweradmin dari browser. Caranya buka chrome atau browser apapun , lalu ketik `192.168.100.1/poweradmin/install` nah bagian ip nya itu sesuaikan dengan ip kamu yang sebelumnya kamu setting pada konfigurasi [virtualbox](debianinstall.md) sebelumnya.

Jika sudah bisa terakses maka akan muncul konfigurasi:
- Di step 1 pilih yang paling atas (bahasa inggris) lalu klik goto step 2
- Di step 2 langsung klik to step 3

- Di step 3 isi saja seperti ini
  - kolom username isi dengan `root`
  - password isi dengan password mariadb di awal tadi
  - database type = mysql
  - hostname `localhost`
  - port `3306`
  - kolom database isi `pdnsdb`
  - DB Charset & DB Collation kosongin aja
  - Poweradmin password ini isi dengan password yang kamu mau misalnya `pdnsadmin`
  - Lalu klik goto step 4

- Konfig step 4 seperti ini
  - Username `root`
  - Password `password` (isi password mariadb kamu tadi)
  - Hostmaster `contoh.xyz` isi aja
  - Primary Nameserver `ns1.contoh.com`
  - Secondary Nameserver `ns2.contoh.com`
  - lalu next ke step 5
  - 
- Step 5
  Copy kode warna merah yang ditampilkan dan paste di mariadb (login mariadb dengan `mariadb -u root -p`)

- Step 6
  - Copy kode yang muncul dari bagian <?php sampe bawah
  - masukkan perintah di debian `nano /var/www/html/poweradmin/inc/config.inc.php`
  - Pastekan kode yang dicopy tadi lalu `CTRL + X` `Y` `ENTER`
  - Lalu go ke step 7
 
- Step 7
klik teks warna biru yaitu PowerAdmin
Lalu nanti akan diarahkan untuk login . masukkan username nya admin dan passwordnya adalah password yang di set untuk poweradmin tadi. dan dengan ini maka konfigurasi telah selesai!
  
