# 🐧 Ubuntu 22.04 LTS Complete Setup Guide

> **System Information:** Ubuntu 22.04 LTS | Username: `lam` | Password: `1`

This comprehensive guide will help you set up a fresh Ubuntu 22.04 system with all essential tools, applications, and configurations for development and daily use.

## 📋 Table of Contents

1. [Prerequisites & Downloads](#1-prerequisites--downloads)
2. [Shell Configuration & Aliases](#2-shell-configuration--aliases)
3. [Git Configuration & SSH Setup](#3-git-configuration--ssh-setup)
4. [Mount Additional Storage Drives](#4-mount-additional-storage-drives)
5. [Essential Software Installation](#5-essential-software-installation)
6. [System Verification](#6-system-verification)

---

## 1. Prerequisites & Downloads

### 📦 Download Required Applications
Access the shared drive containing essential installation files:

**[📁 Drive Link](https://drive.google.com/drive/folders/1vBeH9BclR4kRVLHKMCv8MInC4D85UiVD?usp=sharing)**

> **Note:** Download any specific `.deb` packages or installers from this drive before proceeding.

---

## 2. Shell Configuration & Aliases

### 🔧 Configure Bash Aliases and Functions

Add the following configuration to your `.bashrc` file to enhance your command-line experience:

```bash
# Open bashrc for editing
nano ~/.bashrc

# Add the following content to the end of the file:

# ============================================
# CUSTOM BASH CONFIGURATION
# ============================================

# === Conda utility functions ===
function acti() {
    conda activate "$1"
}

function deacti() {
    conda deactivate
}

function create() {
    python_version="${2:-3.10}"  # Default to Python 3.10 if no version is given
    conda create --name "$1" python="$python_version" -y
}

# === Common aliases ===
alias py='python3'
alias pipall='pip install -r requirements.txt'
alias venv='python3 -m venv venv && source venv/bin/activate'
alias down='cd ~/Downloads'
alias cls='clear'
alias cleanup='sudo apt autoremove -y && sudo apt clean'
alias update='sudo apt update && sudo apt upgrade -y'
alias fixbroken='sudo apt --fix-broken install'

# === Git helper function ===
function gitupdate() {
    if [ -z "$1" ] || [ -z "$2" ]; then
        echo "Usage: gitupdate \"commit message\" branch-name"
        return 1
    fi

    git add .
    git commit -m "$1"
    git push -u origin "$2" -f
}

# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/home/lam/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/home/lam/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/home/lam/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/home/lam/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
```

### 🔄 Apply Changes
```bash
# Reload bashrc to apply changes
source ~/.bashrc
```

---

## 3. Git Configuration & SSH Setup

### 🔐 Configure Git Global Settings

```bash
# Set global Git configuration
git config --global user.name "Himlamtech"
git config --global user.email "hailam04.work@gmail.com"

# Generate SSH key for GitHub authentication
ssh-keygen -t ed25519 -C "hailam04.work@gmail.com"

# Enable credential storage
git config --global credential.helper store
```

### 📋 Add SSH Key to GitHub
1. Copy your public SSH key:
   ```bash
   cat ~/.ssh/id_ed25519.pub
   ```
2. Go to GitHub → Settings → SSH and GPG keys → New SSH key
3. Paste the key and save

---

## 4. Mount Additional Storage Drives

### 💾 Identify and Mount External Drives

```bash
# List all available drives and partitions
lsblk

# Check drive details and file systems
sudo fdisk -l

# Create mount point directory (example for a data drive)
sudo mkdir -p /mnt/data

# Get the UUID of your drive (replace /dev/sdX1 with your actual drive)
sudo blkid /dev/sdX1

# Create backup of fstab
sudo cp /etc/fstab /etc/fstab.backup

# Edit fstab to auto-mount the drive
sudo nano /etc/fstab

# Add line to fstab (replace UUID and adjust options as needed):
# UUID=your-drive-uuid /mnt/data ext4 defaults,noatime 0 2
# For NTFS drives use: UUID=your-drive-uuid /mnt/data ntfs defaults,uid=1000,gid=1000,umask=022 0 0

# Test the fstab configuration
sudo mount -a

# Verify the mount
df -h
```

### 🔍 Alternative: GUI Method (Recommended for beginners)
```bash
# Use the built-in Disks utility
gnome-disks
```
> **Note:** The Disks application provides a user-friendly interface to format and mount drives automatically.

---

## 5. Essential Software Installation

### 📦 System Updates and Prerequisites

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Install essential prerequisites
sudo apt install apt-transport-https ca-certificates curl software-properties-common wget gpg -y
```

### 🔧 Development Tools

#### Git Version Control
```bash
# Install Git
sudo apt install git -y
```

#### Docker & Docker Compose
```bash
# Add Docker's official GPG key and repository
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Update package index and install Docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io -y

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Add current user to docker group (requires logout/login to take effect)
sudo usermod -aG docker $USER
```

#### API Development Tools
```bash
# Install Postman
sudo snap install postman
```

### 🎨 System Customization Tools

```bash
# Install GNOME customization tools
sudo apt install gnome-tweaks gnome-shell-extension-manager dconf-editor -y
```

### 💬 Communication Applications

```bash
# Install communication apps via Snap
sudo snap install telegram-desktop
sudo snap install teams-for-linux
sudo snap install discord
```

### ⚡ System Performance

```bash
# Install zRAM for better memory management
sudo apt install zram-config -y
```

### 🌏 Vietnamese Input Method (ibus-bamboo)

```bash
# Add ibus-bamboo repository
sudo add-apt-repository ppa:bamboo-engine/ibus-bamboo -y

# Update package list
sudo apt update

# Install ibus and ibus-bamboo
sudo apt install ibus ibus-bamboo --install-recommends -y

# Restart ibus
ibus restart

# Set ibus-bamboo as default input method
env DCONF_PROFILE=ibus dconf write /desktop/ibus/general/preload-engines "['BambooUs', 'Bamboo']"
gsettings set org.gnome.desktop.input-sources sources "[('xkb', 'us'), ('ibus', 'Bamboo')]"
```

### 🔄 Post-Installation Steps

```bash
# Clean up package cache
sudo apt autoremove -y && sudo apt autoclean

# Verify Docker installation
docker --version
docker-compose --version

# Verify Git installation
git --version
```

---

## 6. System Verification

### ✅ Verification Checklist

Run the following commands to verify your installation:

```bash
# Check system information
neofetch || (sudo apt install neofetch -y && neofetch)

# Verify installed applications
echo "=== Checking installed applications ==="
git --version
docker --version
docker-compose --version
snap list | grep -E "(postman|telegram|teams|discord)"

# Check mounted drives
echo "=== Mounted drives ==="
df -h

# Check active services
echo "=== Docker service status ==="
sudo systemctl status docker

# Test aliases
echo "=== Testing custom aliases ==="
alias | grep -E "(py|cls|update|cleanup)"
```

### 🎯 Final Configuration Steps

1. **Logout and login** to apply Docker group permissions
2. **Restart the system** to ensure all services start properly
3. **Configure ibus-bamboo** in Settings → Region & Language → Input Sources
4. **Set up GitHub SSH key** as described in Section 3
5. **Test all applications** to ensure they work correctly

### 📝 Troubleshooting

#### Common Issues and Solutions

**Docker permission denied:**
```bash
# If you get permission denied errors with Docker
sudo usermod -aG docker $USER
# Then logout and login again
```

**ibus-bamboo not working:**
```bash
# Restart ibus and reconfigure
ibus restart
im-config -n ibus
```

**Snap applications not starting:**
```bash
# Refresh snap packages
sudo snap refresh
```

---

## 🎉 Congratulations!

Your Ubuntu 22.04 system is now fully configured with:
- ✅ Custom bash aliases and functions
- ✅ Git configuration with SSH keys
- ✅ Docker and development tools
- ✅ Communication applications
- ✅ System customization tools
- ✅ Vietnamese input method
- ✅ Additional storage drives (if configured)

**Next Steps:**
- Install additional software specific to your needs
- Configure your development environment
- Set up your preferred IDE/editor
- Customize your desktop environment

---

## 📚 Additional Resources

- [Ubuntu Official Documentation](https://help.ubuntu.com/)
- [Docker Documentation](https://docs.docker.com/)
- [Git Documentation](https://git-scm.com/doc)
- [GNOME Extensions](https://extensions.gnome.org/)

---

*Last updated: $(date)*