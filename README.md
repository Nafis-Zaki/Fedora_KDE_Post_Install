# Fedora_KDE_Post_Install
A guide on what needs to be done after you install Fedora KDE (especially coming from an Windows user) for 2026.

This document provides a comprehensive set of steps to optimize your Fedora 43 KDE Plasma installation, as demonstrated in the Fedora KDE Post Installation Steps for 2026 video.
1. Set Shortcuts

Customize your workflow by setting keyboard shortcuts in System Settings > Keyboard > Shortcuts. Common optimizations include:

    Terminal: Set Meta + T to launch Konsole.

    Overview: Ensure Meta (Windows key) is mapped to the Plasma Overview.

    Window Management: Configure shortcuts for tiling or moving windows between desktops.

2. Update System

Fedora 43 utilizes DNF5, which is significantly faster than previous versions. Start by optimizing the download speed and performing a full refresh:
Bash

# Optimize DNF5 parallel downloads
sudo nano /etc/dnf/dnf.conf
# Add: max_parallel_downloads=10

# Perform system update
sudo dnf upgrade --refresh

3. Nvidia Driver Setup

If you have an Nvidia graphics card, enable the RPM Fusion repositories and install the proprietary drivers. This is essential for hardware acceleration and gaming:
Bash

# Enable RPM Fusion Free and Non-Free
sudo dnf install https://mirrors.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
                 https://mirrors.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm

# Install Nvidia Drivers and CUDA
sudo dnf install akmod-nvidia xorg-x11-drv-nvidia-cuda

4. Media Codecs

Fedora ships with limited codecs due to licensing. To enable playback for H.264, HEVC, and other formats, swap the default ffmpeg with the full version:
Bash

# Swap to full ffmpeg
sudo dnf swap ffmpeg-free ffmpeg --allowerasing

# Install multimedia groups
sudo dnf group upgrade multimedia --exclude=PackageKit-gstreamer-plugin
sudo dnf group upgrade sound-and-video

5. Firefox Setup

Ensure Firefox can handle all web content by installing the OpenH264 plugin and enabling it in settings:
Bash

sudo dnf install -y openh264 gstreamer1-plugin-openh264 mozilla-openh264
sudo dnf config-manager setopt fedora-cisco-openh264.enabled=1

6. Input Method Configurations

For advanced text input (like Pinyin or Korean) or speech-to-text tools:

    Fcitx5: Install the framework and KDE configuration module:
    Bash

    sudo dnf install fcitx5 fcitx5-configtool kcm-fcitx5 fcitx5-chinese-addons

    Speechnote: This can be installed via Flatpak to provide offline speech recognition and translation.

7. System Backup & Cloud Integration
System Backup

Install Deja Dup (Backups) for simple, scheduled file backups:
Bash

sudo dnf install deja-dup

Cloud Integration (rclone)

Use rclone to mount Google Drive or OneDrive as local folders and set up a systemd service for automation:
Bash

# Install rclone
sudo dnf install rclone

# Configure remotes
rclone config
# (Follow prompts for 'gdrive' or 'onedrive')

# Create a systemd user service for mounting
mkdir -p ~/.config/systemd/user
nano ~/.config/systemd/user/rclone-mount.service

Example Service File:
Ini, TOML

[Unit]
Description=Rclone Mount Service
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount gdrive: ~/Cloud/GDrive --vfs-cache-mode full
Restart=always

[Install]
WantedBy=default.target

Enable it with: systemctl --user enable --now rclone-mount.service.
8. Application Installation

Install the primary application suite using DNF or Flatpak. For Google Antigravity, you must add the official repository first:
Bash

# 1. Add Antigravity Repository
sudo tee /etc/yum.repos.d/antigravity.repo << EOL
[antigravity-rpm]
name=Antigravity RPM Repository
baseurl=https://us-central1-yum.pkg.dev/projects/antigravity-auto-updater-dev/antigravity-rpm
enabled=1
gpgcheck=0
EOL

# 2. Bulk Install Applications
sudo dnf install antigravity google-chrome-stable micro vlc filezilla kate nvtop obs-studio kmymoney yumex-dnf darktable cloudflare-warp

# 3. Flatpak Applications (Recommended for these)
flatpak install flathub md.obsidian.Obsidian
flatpak install flathub dev.zed.Zed
flatpak install flathub com.onlyoffice.desktopeditors

9. Integrate Mail, Calendar, and Services

KDE Plasma integrates directly with online services through KDE Online Accounts in System Settings:

    Add your Google or Microsoft account to sync KMail, KOrganizer (Calendar), and Dolphin with your cloud files.

    Ensure kio-gdrive is installed for seamless file browsing.

10. Podman/Docker & Stirling PDF

Fedora natively supports Podman, an engine for managing containers. You can use it to host a local instance of Stirling PDF:
Bash

# Pull and run Stirling PDF
podman run -d \
  -p 8080:8080 \
  -v ./trainingData:/usr/share/tessdata \
  --name stirling-pdf \
  froodle/s-pdf

Access the tool at http://localhost:8080 to perform advanced PDF operations locally and securely.

This Fedora KDE Post Installation Steps for 2026 video is highly relevant as it provides a visual walkthrough of these specific commands and configurations for the latest Fedora 43 desktop environment.
