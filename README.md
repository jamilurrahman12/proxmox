# Open Cloud Revolution: Hands-On Private Cloud with PROXMOX VE
This guide prepares you to administer a full-fledge cloud infrastructure for an organization of any size.

## Lab Plan Architecture

<img width="970" height="571" alt="image" src="https://github.com/user-attachments/assets/1119e349-bd6c-4d1a-9bf2-9ce906ac9b42" />

In our lab, we will use VMware workstation/Fusion for provisioning the required servers.

## VMware Workstation Installation and Setup

For Hosting 3-Node Proxmox Cluster

The following section provides step-by-step instructions to install VMware Workstation on your computer. VMware Workstation allows you to create and manage virtual machines (VMs) — in this case, we’ll use it to host a 3-node Proxmox cluster and one (01) Proxmox backup server.

### System Requirements

| Requirement             | Minimum                                                            | Recommended           |
| ----------------------- | ------------------------------------------------------------------ | --------------------- |
| **Operating System**    | Windows 8/10/11 (64-bit); Linux or macOS (Intel/Apple Silicon)     | Latest updated OS     |
| **Processor**           | 2-core 64-bit CPU with virtualization support (Intel VT-x / AMD-V) | Quad-core or higher   |
| **RAM**                 | 8 GB                                                               | 16 GB or more         |
| **Disk Space**          | 50 GB free                                                         | 100 GB+ SSD preferred |
| **Internet Connection** | Required for download & updates                                    | Stable broadband      |

⚙️ Tip: Enabling Virtualization in BIOS/UEFI

Before you can create or run virtual machines, your computer’s processor must have hardware virtualization enabled. This allows VMware to run multiple virtual systems efficiently on your computer.

#### Steps to enable CPU Virtualization feature -
1. Restart Your Computer
2. Enter BIOS/UEFI Setup: As soon as your computer starts (before Windows logo appear), press one of the following keys repeatedly:

      * Dell / ASUS / Lenovo / Acer: **F2**
   
      * HP: **Esc or F10**
       
      * MSI / Gigabyte: **Del**
       
      (Tip: Watch for the message “**Press F2/DEL to enter Setup**” on boot screen)

4. Look for settings like CPU / Processor / Advanced Settings / System Configuration
5. Go to any of the above available options and enable **VT-x** for Intel CPU or enable **AMD-V / SVM Mode** for AMD CPU

✅ How to Verify Virtualization is Enabled (**Windows**)

   * Open **Task Manager**
      
   * Go to **Performance** → **CPU** tab
      
   * Check the bottom-right corner
      
   * It should say: “**Virtualization: Enabled**”
      
   * If it shows “**Disabled**”, repeat the steps above

✅ How to Verify Virtualization is Enabled (**MacOS**)

**For Intel Mac:** Open terminal and run the below command.

    sysctl -a | grep machdep.cpu.features
    
Look for the feature flag **VMX** in the output.

If **VMX** is listed → virtualization (Intel VT-x) is supported and enabled.

If **VMX** is missing → your Mac’s CPU is too old to support virtualization.

**For Apple Silicon (M1/M2/M3):** Apple Silicon Macs use a built-in Apple Virtualization Framework instead of Intel VT-x.
You can check support with running the following command in the terminal:

    sysctl hw.optional.arm64
    
If your Mac returns a result without error (e.g., hw.optional.arm64: 1), your system fully supports virtualization.

### VMware Software Download

VMware Workstation Pro runs on Windows and Linux only and is not designed for MacOS; You need to use VMware Fusion, which is available for macOS (both Intel and Apple silicon).

Moreover, Proxmox VE officially supports only 64-bit x86 architecture (Intel 64 or AMD64) for the host operating system. VMware Fusion on Apple Silicon runs ARM VMs. An x86 ISO like Proxmox can't present a compatible EFI bootloader to an ARM UEFI, so you will get “No compatible bootloader found” in your Apple silicon MacOS (M1/M2/M3).

**In summary, you need to have Windows, Linux, or Intel MacOS for completing our lab.**

**For Windows** - https://drive.bdren.net.bd/index.php/s/WgFmXDSC6T3YEPA

**For Linux** - https://drive.bdren.net.bd/index.php/s/yLGM8YpMRgEBQ5z

**For MacOS** - https://drive.bdren.net.bd/index.php/s/om2T99cerZripNe

### Install VMware Workstation / Fusion

Run the downloaded installer file (.exe file in windows; .bundle file in Linux; .dmg file in MacOS) and follow the installation wizard to finish installation.

### Initial Setup of VMware Workstation / Fusion

Launch VMware Workstation / Fusion

In workstation, Go to **Edit** --> **Virtual Network Editor**

In Fusion, Go to **Settings** --> **Network**

It will open an editor wizard. Click on the "Change Settings" button to adjust any network configuration. We need to create/edit 2 types of network adapters. One (01) is **Host-only** type and another is **NAT type**.

1. **VMnet1:** Click on **Host-only** virtual adapter and edit the settings as shown in the following screenshot.

<div align="center">
<img width="600" height="600" alt="image" src="https://github.com/user-attachments/assets/219d8514-c177-4be8-ac66-25ed57cfa724" />
</div>

2. **VMnet8:** Click on **NAT** virtual adapter and edit the settings as shown in the following screenshots. 

<div align="center">
<img width="600" height="500" alt="image" src="https://github.com/user-attachments/assets/9168a8c0-3b99-4bca-b252-7689ee1371fb" />
</div>

Click on the **NAT Settings** button and enter the gateway address. Here, we can't use '1' as a gateway, because the host machine (your laptop) uses '1' as its (virtual adapter) gateway. That's why we need to set '2' or any number (below 255). Obviously, we must use the same IP as the gateway within any VMs (Proxmox nodes).

<div align="center">
<img width="600" height="600" alt="image" src="https://github.com/user-attachments/assets/05897a72-f23c-4fb8-9505-5e4cbc09ed86" />
</div>

## Single Proxmox node Setup

Installing from the Proxmox ISO is the recommended method for both new and existing users. The installer will guide you through the setup, allowing you to partition the local disk(s), apply basic system configurations (such as time zone, language, and network settings), and install all required packages. 

Alternatively, Proxmox VE can be installed on top of an existing Debian system. This option is only recommended for advanced users, as detailed knowledge of Proxmox VE is required.

    apt-get update
    apt-get install proxmox-ve

### Proxmox ISO image Download

Download the Proxmox installer ISO image from: https://drive.bdren.net.bd/index.php/s/BRoHfoetjz5miYC

or, https://www.proxmox.com/en/downloads/proxmox-virtual-environment/iso

### Hardware/System Requirements

•     **CPU:** Intel 64 or AMD64 with Intel VT/AMD-V CPU flag.

•     **RAM:** 2 GB RAM, plus additional RAM needed for guests

•     **Hard disk:** 20 GB

•     At least one network card (NIC)

### Provisioning Hardware (CPU, RAM, Disk, NIC) for Server-1/Node-1/Host -1

Launch VMware Workstation/Fusion app and create a Virtual machine.

A new installtion wizard will be opneded and select the downloaded ISO as installer disc image file.

While choosing the Operating system and its version, select Linux and Debian 13.x 64-bit, depending on the Proxmox version you are using. Proxmox 9 utilizes Debian 13 (Trixie) as its base operating system, whereas Proxmox 8 employed Debian 12 (Bookworm). If Debian 13.x version is not available, select Debian 12.x at least.

<div align="center">
<img width="426" height="427" alt="image" src="https://github.com/user-attachments/assets/ac4dc04e-23d2-4388-aa1b-404d2b8156bd" />
</div>

Set the Name of this virtual machine to **PVE-1 **or Server-1/Node-1/Host -1. Create a new virtual disk (consolidated) with a minimum capacity of 20GB for installing the ISO on this disk.

<div align="center">
<img width="427" height="430" alt="image" src="https://github.com/user-attachments/assets/95b7f4ca-4f03-499c-9d9a-9256ed2772d3" />
</div>

Select the CPU, RAM, and other components according to your available resources, ensuring they meet the minimum requirements. Particularly, go to **Network Adapter** and select the custom adapter that you previously created (**VMnet8 - NAT**).

<div align="center">
<img width="754" height="729" alt="image" src="https://github.com/user-attachments/assets/06b23d82-7c7d-4825-b20d-26e99a2d4457" />
</div>

Go to **Processors** section and enable the option named **Virtualize Intel VT-x/EPT or AMD-V/RVI**. It will enable this VM (PVE-1) to create/host virtual machines on top of it.

<div align="center">
<img width="759" height="734" alt="image" src="https://github.com/user-attachments/assets/e63ebe97-0cb9-4ad1-a495-df5f67099625" />
</div>

### Install Proxmox on Server-1

Power ON the VM (PVE-1). After initial booting, the Proxmox VE menu will be displayed, and one of the following options can be selected. Choose **Install Proxmox VE (Graphical)** to start the installation.

<div align="center">
<img width="1007" height="750" alt="image" src="https://github.com/user-attachments/assets/e16f77db-7f4e-40a6-9967-df44c8910b66" />
</div>

The first step is to read and accept their EULA (End User License Agreement). Following this, you can select the target hard disk(s) for the installation. By default, the whole disk(s) is used and all existing data is removed. The **Options** button lets you select the target file system, which defaults to ext4. Change it to xfs file system and the installer will use LVM.

<div align="center">
<img width="1268" height="789" alt="image" src="https://github.com/user-attachments/assets/584f8c70-a143-4df9-9be1-f7a41ab56db0" />
</div>

The next page asks for basic configuration options like your location, time zone, and keyboard layout. The location is used to select a nearby download server, in order to increase the speed of updates. The installer is usually able to auto-detect these settings, so you only need to change them in rare situations when auto-detection fails, or when you want to use a keyboard layout not commonly used in your country.

<div align="center">
<img width="1144" height="775" alt="image" src="https://github.com/user-attachments/assets/df813619-2d86-472a-9779-a62505a3e586" />
</div>

Next the password of the superuser (root) and an email address needs to be specified. The password must consist of at least 8 characters. It’s highly recommended to use a stronger password. All notification emails will be sent to the specified email address as system administrator.

<div align="center">
<img width="861" height="613" alt="image" src="https://github.com/user-attachments/assets/0293f177-a02f-463e-b3c7-d6a380c71dea" />
</div>

The last step is the network configuration. Network interfaces that are UP show a green-filled circle in front of their name in the drop down menu. Please note that during installation you can either specify an IPv4 or IPv6 address, but not both. To configure a dual stack node, add additional IP addresses after the installation.

<div align="center">
<img width="1270" height="770" alt="image" src="https://github.com/user-attachments/assets/c70d28c6-0c3d-4f51-834e-9e696f68532a" />
</div>


The next step shows a summary of the previously selected options. Please re-check every setting and use the Previous button if a setting needs to be changed. 

<div align="center">
<img width="1275" height="778" alt="image" src="https://github.com/user-attachments/assets/88817614-0efa-4e33-80ff-530481001863" />
</div>

After clicking Install, the installer will begin to format the disks and copy packages to the target disk(s). Please wait until this step has finished; then remove the installation medium and restart your system. Copying the packages usually takes several minutes, mostly depending on the speed of the installation medium and the target disk performance.


### Accessing Proxmox Dashboard (Graphical User Interface)

After a successful installation and reboot of the system you can use the Proxmox VE web interface for further configuration.

Point your browser to the IP address given during the installation and port 8006, for instance: https://10.10.0.10:8006

Initially, you will get Browser Warning for using self-signed certificate. We'll generate Let's Encrypt commercial certificate later. For now, access the dashboard by clicking on **Advanced** option; then **Accept the Rist and Continue**.

Log in using the root (realm PAM) username and the password chosen during installation.

### Host System Administration

The following sections will focus on common virtualization tasks and explain the Proxmox VE specifics regarding the administration and management of the host machine.

### Package Repositories

Proxmox VE uses APT as its package management tool like any other Debian-based system. Repositories are a collection of software packages, they can be used to install new software, but are also important to get new updates for security fixes, bug fixes, and new features.

In Proxmox VE, there are mainly 2 types of repositories available.

1. Enterprise : Upload your active subscription key to gain access to the Enterprise repository. 
2. No Subscription: If you don't buy a subscription. 

It is to be explicitely mentioned that there is no difference between those 2 subscription plan in terms of functionality and features. Rather, the enterprise subscription will ensure the most stable software repository and get prompt technical support.

Select the node and go to Repositories menu. There, you can disable enterprise repository for both pve & ceph sources, and enable **pve-no-subscription** and **ceph-squid-no-subscription** repository. 

<div align="center">
<img width="1196" height="410" alt="image" src="https://github.com/user-attachments/assets/793462f1-00f1-4d0f-be43-7bc7b54009d2" />
</div>


### System Software Updates

Proxmox provides updates on a regular basis for all repositories. After adding the No-Subscription repositories, Click on **Reload** button. Then, go to **Shell** and update the repositories.

    apt-get update
    apt-get dist-upgrade

### Network Configuration

Proxmox VE is using the Linux network stack. This provides a lot of flexibility on how to set up the network on the Proxmox VE nodes. The configuration can be done either via the GUI, or by manually editing the file /etc/network/interfaces, which contains the whole network configuration. 

A Linux bridge interface (commonly called vmbrX) is needed to connect guests to the underlying physical network. It can be thought of as a virtual switch which the guests and physical interfaces are connected to. This section provides how the network can be set up to accommodate different use cases like redundancy with a Bond and VLAN setup. 

The Software Defined Network is an option for more complex virtual networks in Proxmox VE clusters.

####  Choosing network configurations

There are basically 3 types of networks we need. 

  1.  Management and Cluster communication
  2.  VM/CT network
  3.  Ceph Cluster

In VMware workstation panel, under **Settings** option of the VM (PVE-1), add 3 more **Network Adapter** and configure those adapters as shown in the following screenshot.
1st 3 adapters >> VMnet8 (NAT)
4th adapter >> VMnet1 (Host-only)

<div align="center">
<img width="756" height="731" alt="image" src="https://github.com/user-attachments/assets/ba8f40a6-9d13-48ac-b9d6-234c4aac797f" />
</div>

After successfully adding those network adapters, go back to the Proxmox dashboard and select the Network menu. You will see 4 network interfaces listed there.

<img width="1363" height="329" alt="image" src="https://github.com/user-attachments/assets/987fed55-6fce-4a9c-85e8-13da43bf6c61" />


* 1 interface (ens33) will be dedicated to Management and Cluster communication.

* 2 interfaces (ens37 and ens38) will be bound and dedicated for VM/CT network.

* The 4th interface (ens39) will be dedicated to Ceph cluster communication.

The number of interfaces above depends on the available NIC card you have. In production, each type of network should have redundant interfaces.

##### Step 1

First, we need to set the management IP correctly. By default, the installation program creates a single bridge named **vmbr0**, which is connected to the first Ethernet card and the management IP is set on it. To separate networks, we need to set the management IP address directly on an interface (ens33). You can do it via either GUI or CLI.

The shipped configuration in /etc/network/interfaces looks like this - 

```bash
auto lo
iface lo inet loopback

iface ens33 inet manual

iface ens37 inet manual

iface ens38 inet manual

iface ens39 inet manual

auto vmbr0
iface vmbr0 inet static
        address 10.10.0.10/24
        gateway 10.10.0.2
        bridge-ports ens33
        bridge-stp off
        bridge-fd 0

source /etc/network/interfaces.d/*
```

In Shell/CLI, change this file according to the below - 

```bash
auto lo
iface lo inet loopback

auto ens33
iface ens33 inet static
        address 10.10.0.10/24
        gateway 10.10.0.2

iface ens37 inet manual

iface ens38 inet manual

iface ens39 inet manual

source /etc/network/interfaces.d/*
```

After modifying the file, save it and restart the network service.

    service networking restart

Reload the browser page, and now it looks like - 

<img width="1150" height="228" alt="image" src="https://github.com/user-attachments/assets/a108e40d-d132-42ca-b417-efd83558885e" />

##### Step 2

Create a **Linux Bond** with two interfaces (ens37 and ens38), and then create a **Linux Bridge** using that already created Bond to connect VMs/CTs' network. 

A bond is used to make the network fail-safe.

<img width="598" height="319" alt="image" src="https://github.com/user-attachments/assets/42a8e046-74d1-484e-bc0c-d60d3087dd78" />

Bridges are like physical network switches implemented in software. All virtual guests can share a single bridge, or you can create multiple bridges to separate network domains. 

<img width="598" height="290" alt="image" src="https://github.com/user-attachments/assets/c5a33d44-1cf8-48b7-b2f1-3a7b7b10d49b" />

Click on **Apply Configuration** button. The final network configuration looks like -

<img width="1214" height="221" alt="image" src="https://github.com/user-attachments/assets/c35bb873-ac18-4bc4-b29a-5ad0a75083f3" />


Moreover, the whole network configuration might look like in any production cloud - 


*** we'll configure 4th interface (ens39) for Ceph network later. 


### Certificate Management

## Identity and Access Management (IAM)

•	Basics Authentication & Authorization
•	Securing The root Account
•	Permission Management & Privileges
•	Multi-Factor Authentication for Users
•	Groups and Roles


## QEMU/KVM Virtual Machines

## Proxmox Container Toolkit

## Provisioning Server-2 and Server-3

## Cluster Manager: Proxmox VE Cluster setup

•	Clustering Overview
•	Create Cluster
•	Join Cluster Nodes


### Proxmox Cluster File System (pmxcfs)

## Proxmox VE Storage

### Deploy Hyper-Converged Ceph Cluster

## High Availability (HA) - PVE HA Manager

## Software-Defined Network

## Testing

### Live Migration
### High Availability (HA)


## Migration

### VMware to Proxmox
### AWS EC2 to Proxmox
### Proxmox to VMware


## Proxmox VE Firewall

The integrated firewall allows you to filter network packets on any VM or Container interface. Common sets of firewall rules can be grouped into “security groups”.

•	Iptables/Firewall Overview
•	Generated Iptables Rules
•	Host-Specific Firewall Rules
•	VM Specific Firewall Rules
•	Security Group Implementation
•	Proxmox VE Hosts Security
•	Filter Remote IPs


## Disaster Recovery: DC-DR Concept

### VM / Storage Replication

### Local Backup Procedure

     Backup Jobs & Retention Policy

### VM Snapshot & VM Clone

### VM Restore & Snapshot Revert


## Proxmox Backup Server (PBS): Open-Source Enterprise Backup Solution

### Installtion and Configuration

### Integration with Proxmox Cluster

### Backup Job creation and management

### Restoration

```bash
git clone https://github.com/you/repo.git
cd repo && pip install -r requirements.txt
