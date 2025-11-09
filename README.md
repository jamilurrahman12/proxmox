# Open Cloud Revolution: Hands-On Private Cloud with PROXMOX VE

This guide prepares you to administer a full-fledged cloud infrastructure for an organization of any size.

## Lab Plan Architecture

<img width="967" height="566" alt="image" src="https://github.com/user-attachments/assets/788bebd2-1157-4f78-932b-45b0dbab6124" />

In our lab, we will use VMware Workstation/Fusion for provisioning the required servers.

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

The first step is to read and accept their EULA (End User License Agreement). Following this, you can select the target hard disk(s) for the installation. By default, the whole disk(s) is used and all existing data is removed. The **Options** button lets you select the target file system, which defaults to ext4. Change it to XFS file system, and the installer will use LVM.

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

<img width="595" height="291" alt="image" src="https://github.com/user-attachments/assets/3c59429a-6d6f-429f-b412-c27c32c2b315" />


Click on **Apply Configuration** button. The final network configuration looks like -

<img width="1214" height="220" alt="image" src="https://github.com/user-attachments/assets/fe3bd39c-9553-4450-9adc-874d06dd5faa" />



Moreover, in any production Proxmox cloud environment, the whole network configuration might look like - 


*** we'll configure 4th interface (ens39) for Ceph network later. 


### Certificate Management

Each Proxmox VE cluster creates by default its own (self-signed) Certificate Authority (CA) and generates a certificate for each node which gets signed by the aforementioned CA.

pveproxy daemon is responsible for certificates.

#### Methods for certificates

You have the following options for issuing certificate used by pveproxy:

* By default, a node-specific certificate is used. This certificate is signed by the cluster CA and, therefore, is not automatically trusted by browsers and operating systems. 

* Upload Custom Certificate  (e.g., signed by a commercial CA). 

* Use ACME to get a trusted certificate with automatic renewal. One of the ACME providers is Let’s Encrypt.

#### Validation procedures

ACME supports 2 types of validation

http-01 challenge: using a built-in web server
dns-01 challenges: using a DNS plugin


For http-01 challenges:

You have to accept the ToS of Let’s Encrypt to register an account. 
Port 80 of the node needs to be reachable from the internet. 
There must be no other listener on port 80. 
The requested (sub)domain needs to resolve to a public IP of the Node. 



Proxmox VE includes an implementation of the Automatic Certificate Management Environment ACME protocol, allowing Proxmox VE admins to use an ACME provider like Let’s Encrypt for easy setup of TLS certificates, which are accepted and trusted on modern operating systems and web browsers out of the box.

ACME client supports validation of http-01 challenges using a built-in web server and validation of dns-01 challenges using a DNS plugin.

<img width="1059" height="317" alt="image" src="https://github.com/user-attachments/assets/e92e3436-08fc-4368-ba08-49837d715c9d" />


## Identity and Access Management (IAM) - User Management

Proxmox VE supports multiple authentication sources

Linux PAM standard authentication
Integrated Proxmox VE authentication server
LDAP server
Microsoft Active Directory
OpenID Connect Server


•	Basics Authentication & Authorization
•	Securing The root Account
•	Permission Management & Privileges
•	Multi-Factor Authentication for Users
•	Groups and Roles


## QEMU/KVM Virtual Machines

## Proxmox Container Toolkit

## Provisioning Server-2 and Server-3

## Cluster Manager: Proxmox VE Cluster setup

The Proxmox VE cluster manager **pvecm** is a tool to create a group of physical servers. Such a group is called a cluster. We use the Corosync Cluster Engine for reliable group communication. There’s no explicit limit for the number of nodes in a cluster. 

Because we use the Proxmox cluster file system (pmxcfs), you can connect to any node to manage the entire cluster. Each node can manage the entire cluster. There is no need for a dedicated manager node.

### Preparing Nodes

First, install Proxmox VE on all nodes. Ensure that each node is installed with the final hostname and IP address configuration. Changing the hostname and IP is not possible after cluster creation.

•	Clustering Overview
•	Create Cluster



You can either create a cluster on the console (login via ssh), or through the API using the Proxmox VE web interface (Datacenter → Cluster).
Note: Use a unique name for your cluster. This name cannot be changed later. The cluster name follows the same rules as node names.

Under Datacenter → Cluster, click on Create Cluster. Enter the cluster name and select a network connection from the drop-down list to serve as the main cluster network (Link 0). It defaults to the IP resolved via the node’s hostname.


•	Adding Nodes to the Cluster

All existing configuration in /etc/pve is overwritten when joining a cluster. In particular, a joining node cannot hold any guests, since guest IDs could otherwise conflict, and the node will inherit the cluster’s storage configuration. To join a node with existing guest, as a workaround, you can create a backup of each guest (using vzdump) and restore it under a different ID after joining. If the node’s storage layout differs, you will need to re-add the node’s storages, and adapt each storage’s node restriction to reflect on which nodes the storage is actually available.

Log in to the web interface on an existing cluster node. Under Datacenter → Cluster, click the Join Information button at the top. Then, click on the button Copy Information. Alternatively, copy the string from the Information field manually.


Next, log in to the web interface on the node you want to add. Under Datacenter → Cluster, click on Join Cluster. Fill in the Information field with the Join Information text you copied earlier. Most settings required for joining the cluster will be filled out automatically. For security reasons, the cluster password has to be entered manually.



After clicking the Join button, the cluster join process will start immediately. After the node has joined the cluster, its current node certificate will be replaced by one signed from the cluster certificate authority (CA). This means that the current session will stop working after a few seconds. You then might need to force-reload the web interface and log in again with the cluster credentials.

Now your node should be visible under Datacenter → Cluster.

In Shell/CLI, you can run the following to see the cluster status and nodes.

    pvecm status
    pvecm nodes

### Quorum

Proxmox VE use a quorum-based technique to provide a consistent state among all cluster nodes.

A quorum is the minimum number of votes that a distributed transaction has to obtain in order to be allowed to perform an operation in a distributed system.
Proxmox VE assigns a single vote to each node by default.

### Proxmox Cluster File System (pmxcfs)


The Proxmox Cluster file system (“pmxcfs”) is a database-driven file system for storing configuration files, replicated in real time to all cluster nodes using corosync. We use this to store all Proxmox VE related configuration files.

Although the file system stores all data inside a persistent database on disk, a copy of the data resides in RAM. This imposes restrictions on the maximum size, which is currently 128 MiB. This is still enough to store the configuration of several thousand virtual machines.


Technology

We use the Corosync Cluster Engine for cluster communication, and SQlite for the database file. The file system is implemented in user space using FUSE.

 File System Layout

The file system is mounted at:

/etc/pve



## Proxmox VE Storage



The Proxmox VE storage model is very flexible. Virtual machine images can either be stored on one or several local storages, or on shared storage like NFS or iSCSI (NAS, SAN). There are no limits, and you may configure as many storage pools as you like. You can use all storage technologies available for Debian Linux.

One major benefit of storing VMs on shared storage is the ability to live-migrate running machines without any downtime, as all nodes in the cluster have direct access to VM disk images. There is no need to copy VM image data, so live migration is very fast in that case.

The storage library (package libpve-storage-perl) uses a flexible plugin system to provide a common interface to all storage types. This can be easily adopted to include further storage types in the future.


 Storage Types

There are basically two different classes of storage types:

File level storage

    File level based storage technologies allow access to a fully featured (POSIX) file system. They are in general more flexible than any Block level storage (see below), and allow you to store content of any type. ZFS is probably the most advanced system, and it has full support for snapshots and clones.
Block level storage

    Allows to store large raw images. It is usually not possible to store other files (ISO, backups, ..) on such storage types. Most modern block level storage implementations support snapshots and clones. Ceph RADOS is a distributed systems, replicating storage data to different nodes that can be accessed as RBD (RADOS Block Device).


#### Available storage types

<img width="901" height="690" alt="image" src="https://github.com/user-attachments/assets/3002b182-2063-405e-b422-a405242ec65c" />



All Proxmox VE related storage configuration is stored within a single text file at /etc/pve/storage.cfg. As this file is within /etc/pve/, it gets automatically distributed to all cluster nodes. So all nodes share the same storage configuration.

Sharing storage configuration makes perfect sense for shared storage, because the same “shared” storage is accessible from all nodes.


#### Thin Provisioning

A number of storages, and the QEMU image format qcow2, support thin provisioning. With thin provisioning activated, only the blocks that the guest system actually use will be written to the storage.

Say, for instance, you create a VM with a 32GB hard disk, and after installing the guest system OS, the root file system of the VM contains 3 GB of data. In that case only 3GB are written to the storage, even if the guest VM sees a 32GB hard drive. In this way thin provisioning allows you to create disk images which are larger than the currently available storage blocks. You can create large disk images for your VMs, and when the need arises, add more disks to your storage without resizing the VMs' file systems.

All storage types which have the “Snapshots” feature also support thin provisioning.
Caution 	If a storage runs full, all guests using volumes on that storage receive IO errors. This can cause file system inconsistencies and may corrupt your data. So it is advisable to avoid over-provisioning of your storage resources, or carefully observe free space to avoid such conditions.

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
