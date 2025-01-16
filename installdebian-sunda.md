![Debian](https://img.shields.io/badge/Debian-D70A53?style=for-the-badge&logo=debian&logoColor=white) ![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)

# Basa

[Indonesia](installdebian.md) | [English](installdebian-english.md) | [Jawa](installdebian-jawa.md) | Sunda

# Install Debian di VirtualBox  

Wilujeng sumping, abdi NathanKanaeru (Bagas sabenerna). Ieu tutorial kumaha cara masang Debian di VirtualBox (CLI wungkul). Ieu ogé kaasup cara masang SSH sangkan Debian-na bisa diakses tina terminal / PowerShell / naon wae anu dipikaresep. Hayu urang simak sacara taliti supaya teu aya kasalahan!

## Syarat  
1. **VirtualBox**  
   - Unduh jeung pasang VirtualBox tina [situs resmi](https://www.virtualbox.org/). Skip upami geus dipasang.  
2. **Extension Pack**  
   - Unduh jeung tambahkeun Extension Pack tina [halaman download VirtualBox](https://www.virtualbox.org/wiki/Downloads). Ulah hilap diimpor lamun geus diunduh.  
3. **Debian VM Image**  
   - Unduh Debian VM Image tina [VMImage](https://sourceforge.net/projects/linuxvmimages/files/VirtualBox/D/12/Debian_12.0.0_VBM.7z/download). Pilih anu minimum sangkan henteu nyokot loba storage (biasana ngan ukur 3GB-an).  

## Léngkah-Léngkah  

1. **Ekstrak File Debian VM Image**  
   - Saatos diunduh, ekstrak file Debian VM Image anu berformat `.zip` atanapi `.7z`.  
2. **Impor VM ka VirtualBox**  
   - Klik dua kali file anu eksténsina `.vbox` pikeun muka sarta ngimpor Debian ka VirtualBox sacara otomatis.  

3. **Konfigurasi Jaringan di VirtualBox**  
   - Tambihkeun Host-Only Adapter:  
     - Buka **Network** di VirtualBox.  
     - Tambihkeun **Host-Only Adapter** anyar sarta setel IP sakumaha dipikahayang, contona **192.168.100.1**.  
   - Buka pangaturan VM Debian:  
     - **Adapter 1:** Setel kana **NAT**.  
     - **Adapter 2:** Setel kana **Host-Only Adapter** anu tos ditambihkeun tadi.  

4. **Login ka Debian**  
   - Jangkeun VM Debian anu tos diimpor.  
   - Gunakeun akun standar ieu:  
     - **Username:** `debian`  
     - **Password:** `debian`  

5. **Asup ka Root jeung Update Sistem**  
   - Saatos login, asup ka akun root ku paréntah ieu:  
     ```bash
     sudo su
     ```  
   - Update jeung upgrade sistem:  
     ```bash
     apt update && apt upgrade -y
     ```  

6. **Konfigurasi Jaringan di Debian**  
   - Edit file konfigurasi jaringan:  
     ```bash
     nano /etc/network/interfaces
     ```  
   - Tambihkeun konfigurasi ieu:  
     ```  
     auto enp0s8  
     iface enp0s8 inet static  
     address 192.168.100.1/24  
     ```  
   - Restart layanan jaringan:  
     ```bash
     /etc/init.d/networking restart
     ```  

7. **Masang jeung Konfigurasi SSH Server**  
   - Pasang SSH server:  
     ```bash
     apt install openssh-server -y
     ```  
   - Edit file konfigurasi SSH:  
     ```bash
     nano /etc/ssh/sshd_config
     ```  
   - Ganti port SSH nurutkeun kahayang, contona:  
     ```  
     Port 2222
     ```  
   - Restart layanan SSH:  
     ```bash
     systemctl restart sshd
     ```  

8. **Konfigurasi Jaringan di Windows Host**  
   - Buka **Adapter Settings** di Windows.  
   - Pilih adapter Host-Only VirtualBox, klik **Properties**, lajeng setel:  
     - **IP Address:** `192.168.100.2`  
     - **Gateway:** `192.168.100.1`  

9. **Tes Koneksi**  
   - Uji koneksi ku ngalakukeun ping ti Windows ka VirtualBox:  
     ```bash
     ping 192.168.100.1
     ```  
   - Uji koneksi ti VirtualBox ka Windows:  
     ```bash
     ping 192.168.100.2
     ```  

10. **Login nganggo SSH**  
    - Asup ka CMD atawa PowerShell, teras ketik paréntah SSH ieu:  
      ```bash
      ssh debian@192.168.100.1 -p 2222
      ```  
    - Lamun tos asup, jalankeun ieu:  
      ```bash
      sudo su
      apt update -y && apt upgrade -y
      ```  
    - Beres! Instalasi geus réngsé, tinggal nuturkeun tutorial saterusna ngeunaan [PowerDNS Setup](pdnsinstall.md).  

> **Catetan:**  
> - Pastikeun pangaturan jaringan leres sangkan sambungan antara host jeung VM lancar.  
> - SSH ngamungkinkeun aksés jarak jauh ka Debian, jadi pastikeun port anu dipaké henteu bentrok jeung aplikasi séjén.  

# Léngkah Saterusna  
Lamun sagala anu di luhur geus leres, ayeuna urang ngalih ka [Konfigurasi PowerDNS & Admin Panel](pdnsinstall.md). Klik kanggo tutorial lengkepna!  

# Tukang nyieun

dijieun kalayan cinta ku bagaszkyn

## Pasreng Team  
- [BagasZkyn/NathanKanaeru]()  
- [AmbonBlckAndi]()  
- [HastaAdh]()  
- [Alip]()  
- [Andhyka]()  
- [PalWokk]()  
- [M.Agung]()  
