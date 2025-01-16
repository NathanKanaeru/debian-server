![Debian](https://img.shields.io/badge/Debian-D70A53?style=for-the-badge&logo=debian&logoColor=white) ![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)

# Language

[Indonesia](installdebian.md) | English | [Jawa](installdebian-jawa.md) | [Sunda](installdebian-sunda.md)

# Install Debian in VirtualBox  

Hello, I'm NathanKanaeru (Bagas in real life). This is a tutorial on how to install Debian in VirtualBox (CLI only). It also includes how to set up SSH so you can access Debian from the terminal, PowerShell, or any other tool you prefer. Let’s dive in—don’t skip anything to avoid errors!  

## Requirements  
1. **VirtualBox**  
   - Download and install VirtualBox from the [official site](https://www.virtualbox.org/). Skip this step if you’ve already installed it.  
2. **Extension Pack**  
   - Download and add the Extension Pack from the [VirtualBox download page](https://www.virtualbox.org/wiki/Downloads). Don’t forget to import it after downloading.  
3. **Debian VM Image**  
   - Download the Debian VM Image from [VMImage](https://sourceforge.net/projects/linuxvmimages/files/VirtualBox/D/12/Debian_12.0.0_VBM.7z/download). Choose the minimal version to save storage space (it’s usually around 3GB).  

## Steps  

1. **Extract the Debian VM Image**  
   - After downloading, extract the Debian VM Image file in `.zip` or `.7z` format.  
2. **Import the VM into VirtualBox**  
   - Double-click the `.vbox` file to automatically open and import Debian into VirtualBox.  

3. **Network Configuration in VirtualBox**  
   - Add a Host-Only Adapter:  
     - Open the **Network** section in VirtualBox.  
     - Add a **Host-Only Adapter** and configure the IP address, e.g., **192.168.100.1**.  
   - Open the Debian VM settings:  
     - **Adapter 1:** Set to **NAT**.  
     - **Adapter 2:** Set to the **Host-Only Adapter** you added earlier.  

4. **Log in to Debian**  
   - Start the imported Debian VM.  
   - Use the default credentials:  
     - **Username:** `debian`  
     - **Password:** `debian`  

5. **Switch to Root and Update the System**  
   - After logging in, switch to the root account using:  
     ```bash
     sudo su
     ```  
   - Update and upgrade the system:  
     ```bash
     apt update && apt upgrade -y
     ```  

6. **Configure Networking in Debian**  
   - Edit the network configuration file:  
     ```bash
     nano /etc/network/interfaces
     ```  
   - Add the following configuration:  
     ```  
     auto enp0s8  
     iface enp0s8 inet static  
     address 192.168.100.1/24  
     ```  
   - Restart the networking service:  
     ```bash
     /etc/init.d/networking restart
     ```  

7. **Install and Configure SSH Server**  
   - Install the SSH server:  
     ```bash
     apt install openssh-server -y
     ```  
   - Edit the SSH configuration file:  
     ```bash
     nano /etc/ssh/sshd_config
     ```  
   - Change the SSH port to your desired value, e.g.:  
     ```  
     Port 2222
     ```  
   - Restart the SSH service:  
     ```bash
     systemctl restart sshd
     ```  

8. **Configure Windows Host Network**  
   - Open **Adapter Settings** in Windows.  
   - Select the Host-Only VirtualBox adapter, click **Properties**, and configure:  
     - **IP Address:** `192.168.100.2`  
     - **Gateway:** `192.168.100.1`  

9. **Test the Connection**  
   - Test the connection by pinging from Windows to VirtualBox:  
     ```bash
     ping 192.168.100.1
     ```  
   - Test the connection from VirtualBox to Windows:  
     ```bash
     ping 192.168.100.2
     ```  

10. **Login via SSH**  
    - Open CMD or PowerShell, and use the following SSH command to log in to Debian:  
      ```bash
      ssh debian@192.168.100.1 -p 2222
      ```  
    - After logging in, run:  
      ```bash
      sudo su
      apt update -y && apt upgrade -y
      ```  
    - Done! The installation is complete. You can now proceed to the next tutorial on [PowerDNS Setup](pdnsinstall.md).  

> **Note:**  
> - Ensure the network configuration is correct to allow seamless communication between the host and VM.  
> - SSH enables remote access to Debian, so ensure the port used does not conflict with other applications.  

# Next Steps  
If everything above works correctly, proceed to the [PowerDNS & Admin Panel Configuration](pdnsinstall.md) tutorial. Click for the complete guide!  

## Pasreng Team  
- [BagasZkyn/NathanKanaeru]()  
- [AmbonBlckAndi]()  
- [HastaAdh]()  
- [Alip]()  
- [Andhyka]()  
- [PalWokk]()  
- [M.Agung]()  
