# 🧠 STAR-CCM+ Setup on AWS (Ubuntu Server 24.04 LTS - HPC6a Instance)

## 1. Create and Configure the Instance

- Select **Ohio region** – hpc6a is only available here (as far as I know).  
- Allocate **minimum 80 GB EBS storage** (might be able to get away with less, but changing EBS size later is aids).  
- EBS only costs per GB / month, so it’s not too expensive.  
- Choose **Ubuntu Server 24.04 LTS** as the AMI.  
- Choose the **hpc6a** instance type.  
  - The **HPC6a** has **96 vCPUs** and costs around **$2.10/hour** (on-demand).  
  - It’s optimized for high-performance computing workloads like CFD (perfect for STAR-CCM+).  
- Launch the instance and connect via SSH once it’s running.

### SSH Connection Command
```bash
ssh -i your-key.pem ubuntu@<your-instance-public-dns>
```

---

## 2. Install and Set Up XFCE and XRDP

Install a lightweight desktop environment and RDP server:

```bash
sudo apt update -y
sudo apt upgrade -y
sudo apt install -y xfce4 xfce4-goodies xrdp
sudo systemctl enable xrdp
sudo systemctl start xrdp
echo xfce4-session > ~/.xsession
```

### Enable RDP Access
- Open **port 3389** in your AWS security group to allow RDP connections.  
- You can now connect to your instance using **Windows Remote Desktop (RDP)** or any RDP client with your instance’s **public IP address**.

---

## 3. Download and Install STAR-CCM+

### Download the STAR-CCM+ tar.gz file
*(Replace the link below with your actual download link.)*
```bash
wget https://path.to/starccm-version.tar.gz
```

### Extract the archive
```bash
tar -xvzf starccm-version.tar.gz
```

### Navigate into the extracted directory
```bash
cd starccm-version/
```

### Make the installer executable
```bash
chmod +x starccm.aol
```

### Run the installer
```bash
./starccm.aol
```

Follow the on-screen installation prompts.

---

## 4. Notes

- You can change the EBS volume size only by creating a snapshot and restoring to a larger volume later — that’s why 80 GB is a safe minimum.  
- EBS is billed per GB/month, so it’s fine to allocate a bit more initially.  
- Once installed, you can start STAR-CCM+ from terminal or via the desktop session.  
- Stop the instance when not in use to avoid hourly charges.

---

✅ **Done!**  
You now have **STAR-CCM+** installed and ready to use on your **Ubuntu Server 24.04 LTS (HPC6a)** AWS instance.
