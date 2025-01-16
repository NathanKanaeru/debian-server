# Install Debian di VirtualBox  

Panduan langkah demi langkah untuk menginstal Debian di VirtualBox dengan pengaturan jaringan NAT dan Host-Only Adapter.  

## Persyaratan  
1. **VirtualBox**  
   - Unduh dan instal VirtualBox dari [situs resmi](https://www.virtualbox.org/).  
2. **Extension Pack**  
   - Unduh dan tambahkan Extension Pack dari [halaman download VirtualBox](https://www.virtualbox.org/wiki/Downloads).  
3. **Debian VM Image**  
   - Unduh Debian VM Image dari [VMImage](https://www.osboxes.org/debian/).  

## Langkah-Langkah  

1. **Ekstrak File Debian VM Image**  
   - Setelah diunduh, ekstrak file Debian VM Image yang berformat `.zip` atau `.7z`.  
2. **Import VM ke VirtualBox**  
   - Klik dua kali file dengan ekstensi `.vbox` untuk membuka dan mengimpor Debian ke VirtualBox secara otomatis.  

3. **Konfigurasi Jaringan di VirtualBox**  
   - Tambahkan Host-Only Adapter:  
     - Buka **File > Host Network Manager** di VirtualBox.  
     - Tambahkan **Host-Only Adapter** baru dan konfigurasikan IP sesuai kebutuhan.  
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
     su
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

[!NOTE]
- Pastikan pengaturan jaringan sudah benar agar koneksi antara host dan VM berjalan dengan baik.  
- SSH memungkinkan akses jarak jauh ke Debian, jadi pastikan port yang digunakan tidak bertabrakan dengan aplikasi lain.  

Selamat mencoba!  
