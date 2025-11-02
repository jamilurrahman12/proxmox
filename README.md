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

⚙️ Tip: Enable virtualization in BIOS/UEFI.

Look for settings like Intel VT-x, Intel Virtualization Technology, or SVM Mode and enable them. AMD CPU requires AMD-V. Intel CPU requires VT-x

We can download the software from here.

For Windows - https://drive.bdren.net.bd/index.php/s/WgFmXDSC6T3YEPA

For Mac OS - https://drive.bdren.net.bd/index.php/s/om2T99cerZripNe



```bash
git clone https://github.com/you/repo.git
cd repo && pip install -r requirements.txt
