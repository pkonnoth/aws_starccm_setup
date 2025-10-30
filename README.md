Sure — here’s the **raw Markdown file content**, ready to copy directly into a `.md` file (no extra formatting around it):

---

# 🧠 STAR-CCM+ Setup on AWS (Ubuntu Server 24.04 LTS - HPC6a Instance)

## 1. Create and Configure the Instance

* Select **Ohio region** – hpc6a is only available here (as far as I know).
* Allocate **minimum 80 GB EBS storage** (might be able to get away with less, but changing EBS size later is aids).
* EBS only costs per GB / month, so it’s not too expensive.
* Choose **Ubuntu Server 24.04 LTS** as the AMI.
* Choose the **hpc6a** instance type.

  * The **HPC6a** has **96 vCPUs** and costs around **~$2.10/hour** (on-demand).
  * It’s optimized for high-performance computing workloads like CFD (perfect for STAR-CCM+).
* Launch the instance and connect via SSH once it’s running.

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

* Open **port 3389** in your AWS security group to allow RDP connections.
* You can now connect to your instance using **Windows Remote Desktop (RDP)** or any RDP client with your instance’s **public IP address**.

### ⚠️ Set a Password for XRDP Login

Since AWS instances use key-based SSH authentication, no password exists by default — but XRDP requires one.
Set a password for the default Ubuntu user with:

```bash
sudo passwd ubuntu
```

Then enter and confirm a password when prompted.
You will use this password to log in via Remote Desktop.

When connecting with RDP:

* **Username:** `ubuntu`
* **Password:** *(the one you just created)*
* **Session:** Select `Xorg` if prompted

---

## 3. Download and Install STAR-CCM+

### Download STAR-CCM+ from Siemens

1. Go to the official **[Siemens STAR-CCM+ download page](https://plm.sw.siemens.com/en-US/simcenter/fluids/star-ccm-star-cd/)**.
2. **Sign in** with your Siemens account (license or trial required).
3. Download the desired version’s `.tar.gz` installer file for **Linux**.
4. Upload or transfer the file to your AWS instance using `scp` or another method.

Example upload from local machine:

```bash
scp -i your-key.pem starccm-version.tar.gz ubuntu@<your-instance-public-dns>:~
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

## 4. Create a Desktop Launcher for STAR-CCM+

Once STAR-CCM+ is installed, you can create a **desktop icon** (launcher) so you can open it easily from your GUI session.

### Step 1 – Create the `.desktop` File

Create a new launcher file in your applications directory:

```bash
mkdir -p ~/.local/share/applications
nano ~/.local/share/applications/starccm.desktop
```

Paste the following contents inside the file (adjust paths if your STAR-CCM+ version or directory differs):

```ini
[Desktop Entry]
Version=1.0
Type=Application
Name=STAR-CCM+
Comment=Launch Siemens STAR-CCM+
Exec=/opt/Siemens/20.04.007/STAR-CCM+20.04.007/star/bin/starccm+
Icon=/opt/Siemens/20.04.007/STAR-CCM+20.04.007/star/bin/starfish.png
Terminal=false
Categories=Science;Engineering;
```

### Step 2 – Make the Launcher Executable

```bash
chmod +x ~/.local/share/applications/starccm.desktop
```

### Step 3 – (Optional) Add It to Your Desktop

If you also want the icon directly on your desktop:

```bash
cp ~/.local/share/applications/starccm.desktop ~/Desktop/
chmod +x ~/Desktop/starccm.desktop
```

If Ubuntu shows a warning when you first click it, right-click the icon → **Allow Launching**.

---

## 5. Notes

* You can change the EBS volume size only by creating a snapshot and restoring to a larger volume later — that’s why 80 GB is a safe minimum.
* EBS is billed per GB/month, so it’s fine to allocate a bit more initially.
* Once installed, you can start STAR-CCM+ from terminal or via the desktop session.
* Stop the instance when not in use to avoid hourly charges.

---

✅ **Done!**
You now have **STAR-CCM+** installed and ready to use on your **Ubuntu Server 24.04 LTS (HPC6a)** AWS instance, complete with a desktop icon for easy launching.
