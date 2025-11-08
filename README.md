# Open Cloud Revolution: Hands-On Private Cloud with PROXMOX VE
This guide prepares you to administer a full-fledge cloud infrastructure for an organization of any size.

## Lab Plan Architecture

<img width="2445" height="1429" alt="image" src="https://github.com/user-attachments/assets/1c61d951-50d7-406b-85ce-236621d71545" />


## VMware Workstation Installation and Setup

For Hosting 3-Node Proxmox Cluster

The following section provides step-by-step instructions to install VMware Workstation on your computer. VMware Workstation allows you to create and manage virtual machines (VMs) — in this case, we’ll use it to host a 3-node Proxmox cluster and one (01) Proxmox backup server.

### VMware Software Download

VMware Workstation Pro is not designed for MacOS; it runs on Windows and Linux. You need to use VMware Fusion, which is available for macOS (both Intel and Apple silicon)

**For Windows** - https://drive.bdren.net.bd/index.php/s/WgFmXDSC6T3YEPA

**For MacOS** - https://drive.bdren.net.bd/index.php/s/om2T99cerZripNe

### System Requirements

| Requirement             | Minimum                                                            | Recommended           |
| ----------------------- | ------------------------------------------------------------------ | --------------------- |
| **Operating System**    | Windows 8/10/11 (64-bit) or macOS (Intel/Apple Silicon)            | Latest updated OS     |
| **Processor**           | 2-core 64-bit CPU with virtualization support (Intel VT-x / AMD-V) | Quad-core or higher   |
| **RAM**                 | 8 GB                                                               | 16 GB or more          |
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

### Install VMware Workstation / Fusion

Run the downloaded installer file (.exe file in windows; .dmg file in MacOS) and follow the installation wizard to finish installation.

### Initial Setup of VMware Workstation / Fusion

Launch VMware Workstation / Fusion

Go to **Edit** → **Virtual Network Editor**

It will open an editor wizard. Click on the "Change Settings" button to adjust any network configuration. We need to create/edit 2 types of network adapters. One (01) is **Host-only** type and another is **NAT** type.

1. Click on **Host-only** virtual adapter and edit the settings as shown in the following screenshot.

<div align="center">
<img width="600" height="600" alt="image" src="https://github.com/user-attachments/assets/219d8514-c177-4be8-ac66-25ed57cfa724" />
</div>

2. Click on **NAT** virtual adapter and edit the settings as shown in the following screenshots. 

<div align="center">
<img width="600" height="500" alt="image" src="https://github.com/user-attachments/assets/9168a8c0-3b99-4bca-b252-7689ee1371fb" />
</div>

Here, we can't use '1' as a gateway, because the host machine uses '1' as its (virtual adapter) gateway. That's why we need to set '2' or any number (below 255). Obviously, we need to set this IP as the gateway within any VMs (Proxmox nodes).

<div align="center">
<img width="600" height="500" alt="image" src="https://github.com/user-attachments/assets/05897a72-f23c-4fb8-9505-5e4cbc09ed86" />
</div>

## Single Proxmox node Setup

### Hardware Requirements

### Provisioning Hardware (CPU, RAM, Disk, NIC) for Server-1/Node-1/Host -1

### Proxmox ISO image Download

### Attach the ISO as USB or CD/DVD and Install Proxmox on Server-1

### Proxmox Dashboard (Graphical User Interface)

### Host System Administration

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
