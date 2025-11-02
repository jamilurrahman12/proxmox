# Open Cloud Revolution: Hands-On Private Cloud with PROXMOX VE
This guide prepares you to administer a full-fledge cloud infrastructure for an organization of any size.

## VMware Workstation Installation and Setup

For Hosting 3-Node Proxmox Cluster

The following section provides step-by-step instructions to install VMware Workstation on your computer. VMware Workstation allows you to create and manage virtual machines (VMs) — in this case, we’ll use it to host a 3-node Proxmox cluster and one (01) Proxmox backup server.

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

    1. Restart Your Computer
    2. Enter BIOS/UEFI Setup: As soon as your computer starts (before Windows or macOS logo appears), press one of the following keys repeatedly:
       * Dell / ASUS / Lenovo / Acer: F2
       * HP: Esc or F10
       * MSI / Gigabyte: Del
    (Tip: Watch for the message “Press F2/DEL to enter Setup” on boot screen.)

    3. Look for settings like CPU / Processor / Advanced Settings / System Configuration
    4. Go to any of the above available options and enable VT-x for Intel CPU or enable AMD-V / SVM Mode for AMD CPU

✅ How to Verify Virtualization is Enabled (Windows)

    * Open Task Manager.
    * Go to Performance → CPU tab.
    * Check the bottom-right corner.
    * It should say: “Virtualization: Enabled”
    * If it shows “Disabled”, repeat the steps above.

We can download the software from here.

For Windows - https://drive.bdren.net.bd/index.php/s/WgFmXDSC6T3YEPA

For Mac OS - https://drive.bdren.net.bd/index.php/s/om2T99cerZripNe



```bash
git clone https://github.com/you/repo.git
cd repo && pip install -r requirements.txt
