![Debian](https://img.shields.io/badge/Debian-D70A53?style=for-the-badge&logo=debian&logoColor=white) ![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)

# Basa

[Indonesia](installdebian.md) | [English](installdebian-english.md) | Jawa | [Sunda](installdebian-sunda.md)

# Install Debian nang VirtualBox  

Sugeng rawuh, aku NathanKanaeru (jenenge asline Bagas). Iki tutorial carane masang Debian nang VirtualBox (CLI thok). Iki uga kalebu carane masang SSH ben Debian-ne iso diakses nganggo terminal / PowerShell / opo wae sing disenengi. Monggo dipirsani apik-apik ben ora ono kesalahan!  

## Syarat  
1. **VirtualBox**  
   - Download lan pasang VirtualBox saka [situs resmi](https://www.virtualbox.org/). Skip yen wis dipasang.  
2. **Extension Pack**  
   - Download lan tambahi Extension Pack saka [halaman download VirtualBox](https://www.virtualbox.org/wiki/Downloads). Aja lali diimpor yen wis didownload.  
3. **Debian VM Image**  
   - Download Debian VM Image saka [VMImage](https://sourceforge.net/projects/linuxvmimages/files/VirtualBox/D/12/Debian_12.0.0_VBM.7z/download). Pilih sing minimum ben ora akeh mangan storage (biasane mung udakara 3GB-an).  

## Langkah-Langkah  

1. **Ekstrak File Debian VM Image**  
   - Sawise didownload, ekstrak file Debian VM Image sing formaté `.zip` utawa `.7z`.  
2. **Impor VM nang VirtualBox**  
   - Klik pindho file sing ekstensi `.vbox` kanggo mbukak lan ngimpor Debian nang VirtualBox otomatis.  

3. **Konfigurasi Jaringan nang VirtualBox**  
   - Tambahna Host-Only Adapter:  
     - Bukak **Network** nang VirtualBox.  
     - Tambahna **Host-Only Adapter** anyar lan setel IP sakarepmu, umpamané **192.168.100.1**.  
   - Bukak pengaturan VM Debian:  
     - **Adapter 1:** Setel dadi **NAT**.  
     - **Adapter 2:** Setel dadi **Host-Only Adapter** sing wis ditambahké mau.  

4. **Login nang Debian**  
   - Jupuk VM Debian sing wis diimpor.  
   - Gunakake akun standar iki:  
     - **Username:** `debian`  
     - **Password:** `debian`  

5. **Mlebu dadi Root lan Update Sistem**  
   - Sawise login, mlebu dadi akun root nganggo printah iki:  
     ```bash
     sudo su
     ```  
   - Update lan upgrade sistem:  
     ```bash
     apt update && apt upgrade -y
     ```  

6. **Konfigurasi Jaringan nang Debian**  
   - Edit file konfigurasi jaringan:  
     ```bash
     nano /etc/network/interfaces
     ```  
   - Tambahna konfigurasi iki:  
     ```  
     auto enp0s8  
     iface enp0s8 inet static  
     address 192.168.100.1/24  
     ```  
   - Restart layanan jaringan:  
     ```bash
     /etc/init.d/networking restart
     ```  

7. **Pasang lan Konfigurasi SSH Server**  
   - Pasang SSH server:  
     ```bash
     apt install openssh-server -y
     ```  
   - Edit file konfigurasi SSH:  
     ```bash
     nano /etc/ssh/sshd_config
     ```  
   - Ganti port SSH manut karepmu, umpamané:  
     ```  
     Port 2222
     ```  
   - Restart layanan SSH:  
     ```bash
     systemctl restart sshd
     ```  

8. **Konfigurasi Jaringan nang Windows Host**  
   - Bukak **Adapter Settings** nang Windows.  
   - Pilih adapter Host-Only VirtualBox, klik **Properties**, banjur setel:  
     - **IP Address:** `192.168.100.2`  
     - **Gateway:** `192.168.100.1`  

9. **Tes Koneksi**  
   - Uji koneksi nganggo ping saka Windows menyang VirtualBox:  
     ```bash
     ping 192.168.100.1
     ```  
   - Uji koneksi saka VirtualBox menyang Windows:  
     ```bash
     ping 192.168.100.2
     ```  

10. **Login nganggo SSH**  
    - Mlebu nang CMD utawa PowerShell, banjur ketik printah SSH iki:  
      ```bash
      ssh debian@192.168.100.1 -p 2222
      ```  
    - Yen wis mlebu, lakokna iki:  
      ```bash
      sudo su
      apt update -y && apt upgrade -y
      ```  
    - Beres! Instalasi wis rampung, saiki lanjut nang tutorial sabanjure bab [PowerDNS Setup](pdnsinstall.md).  

> **Cathetan:**  
> - Pastèkna pangaturan jaringan bener ben sambungan antarane host lan VM lancar.  
> - SSH maringi akses jarak adoh nang Debian, mula pastèkna port sing digunakaké ora tabrakan karo aplikasi liyané.  

# Langkah Sabanjuré  
Yen kabèh sing ing dhuwur wis bener, saiki kita pindhah menyang [Konfigurasi PowerDNS & Admin Panel](pdnsinstall.md). Klik kanggo tutorial lengkapé!  

# Sing gawe

Digawe nganggo hati dening BagasZakyan

## Pasreng Tim  
- [BagasZkyn/NathanKanaeru]()  
- [AmbonBlckAndi]()  
- [HastaAdh]()  
- [Alip]()  
- [Andhyka]()  
- [PalWokk]()  
- [M.Agung]()  
