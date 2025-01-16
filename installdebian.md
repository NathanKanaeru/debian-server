![Debian](https://img.shields.io/badge/Debian-D70A53?style=for-the-badge&logo=debian&logoColor=white) ![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)

# Bahasa

Indonesia | [English](bahasa/inggris.md) | [Jawa](Bahasa/jawa.md) | [Sunda](sunda.md)

# Install Debian di VirtualBox  

Halo saya NathanKanaeru (bagas aslinya) . ini adalah tutorial gimana cara install debian di virtualbox (CLI Only) . ini juga include cara install ssh biar debiannya bisa diakses dari terminal / powershell / apalah terserah. Allright simak baik baik dan jangan sampe ada yang kelewatan biar ga eror

## Persyaratan  
1. **VirtualBox**  
   - Unduh dan instal VirtualBox dari [situs resmi](https://www.virtualbox.org/). Skip jika udah install
2. **Extension Pack**  
   - Unduh dan tambahkan Extension Pack dari [halaman download VirtualBox](https://www.virtualbox.org/wiki/Downloads). Jangan lupa diimport kalo udah didownload
3. **Debian VM Image**  
   - Unduh Debian VM Image dari [VMImage](https://sourceforge.net/projects/linuxvmimages/files/VirtualBox/D/12/Debian_12.0.0_VBM.7z/download). Pilih yang minimum biar ga makan banyak storage (paling cuman 3gb an kalo yang minimum tuh)

## Langkah-Langkah  

1. **Ekstrak File Debian VM Image**  
   - Setelah diunduh, ekstrak file Debian VM Image yang berformat `.zip` atau `.7z`.  
2. **Import VM ke VirtualBox**  
   - Klik dua kali file dengan ekstensi `.vbox` untuk membuka dan mengimpor Debian ke VirtualBox secara otomatis.  

3. **Konfigurasi Jaringan di VirtualBox**  
   - Tambahkan Host-Only Adapter:  
     - Buka **Network** di VirtualBox.  
     - Tambahkan **Host-Only Adapter** baru dan konfigurasikan IP sesuai kebutuhan misalnya **192.168.100.1**
   - Buka pengaturan VM Debian:  
     - **Adapter 1:** Atur ke **NAT**.  
     - **Adapter 2:** Atur ke **Host-Only Adapter** yang telah ditambahkan sebelumnya.  

4. **Login ke Debian**  
   - Jalankan VM Debian yang telah diimpor.  
   - Gunakan kredensial default berikut:  
     - **Username:** `debian`  
     - **Password:** `debian`  

5. **Masuk ke Root dan Update Sistem**  
   - Setelah login, masuk ke akun root dengan perintah:  
     ```bash
     sudo su
     ```  
   - Update dan upgrade sistem:  
     ```bash
     apt update && apt upgrade -y
     ```  

6. **Konfigurasi Jaringan di Debian**  
   - Edit file konfigurasi jaringan:  
     ```bash
     nano /etc/network/interfaces
     ```  
   - Tambahkan konfigurasi berikut:  
     ```  
     auto enp0s8  
     iface enp0s8 inet static  
     address 192.168.100.1/24  
     ```  
   - Restart layanan jaringan:  
     ```bash
     /etc/init.d/networking restart
     ```  

7. **Install dan Konfigurasi SSH Server**  
   - Instal SSH server:  
     ```bash
     apt install openssh-server -y
     ```  
   - Edit file konfigurasi SSH:  
     ```bash
     nano /etc/ssh/sshd_config
     ```  
   - Ubah port SSH sesuai keinginan, contohnya:  
     ```  
     Port 2222
     ```  
   - Restart layanan SSH:  
     ```bash
     systemctl restart sshd
     ```  

8. **Konfigurasi Jaringan di Windows Host**  
   - Buka **Adapter Settings** di Windows.  
   - Pilih adapter Host-Only VirtualBox, klik **Properties**, lalu konfigurasikan:  
     - **IP Address:** `192.168.100.2`  
     - **Gateway:** `192.168.100.1`  

9. **Pengujian Koneksi**  
   - Uji koneksi dengan melakukan ping dari Windows ke VirtualBox:  
     ```bash
     ping 192.168.100.1
     ```  
   - Uji koneksi dari VirtualBox ke Windows:  
     ```bash
     ping 192.168.100.2
     ```  
10. **Login dengan SSH**
    - Masuk ke cmd / powershell dan masukkan perintah ssh berikut untuk login ke debian:
      ```
      ssh debian@192.168.100.1 -p 2222
      ```
      untuk `debian` adalah username dari debian tadi dan `192.168.100.1` adalah ip dari yang kita set di adapter tadi lalu `2008` adalah port SSH dari debian tadi yang sebelumnya juga udah kita set. sesuaikan saja sama yang kamu set biar tidak ada kendala
   - Jika sudah maka jalankan :
     ```
     sudo su
     apt update -y && apt upgrade -y
     ```
     dan dengan ini maka instalasi selesai dan bisa lanjut ke tutorial berikutnya di [Powerdns Setup](pdnsinstall.md)


> [!NOTE]
> - Pastikan pengaturan jaringan sudah benar agar koneksi antara host dan VM berjalan dengan baik.  
> - SSH memungkinkan akses jarak jauh ke Debian, jadi pastikan port yang digunakan tidak bertabrakan dengan aplikasi lain.
> - Karena ini CLI bang [Kepleh]() pasti suka

# Langkah berikutnya
Jika semua diatas sudah berhasil kini kita beralih ke [Konfigurasi powerdns & admin panel](pdnsinstall.md) klik untuk tutorial lengkapnya

## Pasreng Team
- [BagasZkyn/NathanKanaeru]()
- [AmbonBlckAndi]()
- [HastaAdh]()
- [Alip]()
- [Andhyka]()
- [PalWokk]()
- [M.Agung]()
