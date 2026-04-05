# Wayland
This is a kindergarten guide.  I'm gonna break down everything cause I'm tired of guides that just tell you to do stuff and you have no idea what you are doing.
### 0. Pre-setup
- Flash your SD card with Raspberry Pi OS Lite (64 bit)
  - Setup WiFi (or use ethernet), user and password, and make sure SSH is enabled
  - 32 bit might work.  Who knows?  Not me.
- Setup your pi and monitor
  - You might need a mouse and keyboard for the initial setup.  This guide assumes that you are doing everything through SSH.  But stuff updates, stuff breaks, you might have a different setup.  Having a keyboard and mouse can make troubleshooting easier.  
Basic Commands
- `sudo` - Elevates the command priviledge.  Allows you to do things that change the system that regular permissions wouldn't allow.
- `apt` - The package manager for Debian.  It will grab software from pre-configured sources and install them on your system.
- `nano` - Lightweight text editor.  To save a file, ctrl + o.  To exit, ctrl + x.

### 1. Update and Install
- SSH into your pi.
  - I used windows + Putty.  Mac and linux have command line SSH built in.  Your pi's IP address will be displayed on the monitor or you can check your router the IP it was assigned.
- Make the pi login automatically on startup.  This will be a kiosk.  I didn't even want to setup a login screen.  
```bash
sudo raspi-config
```  
System Options > Auto Login > Yes > Exit Raspberry Pi Config
- Update the pi.  The imager OS is from 2025-12-04.  Not that long ago but long enough to update some stuff.
```bash
sudo apt update
sudo apt upgrade -y
```
- Install the Software
  - This is the first set of software, just to get a desktop going.
labwc is a wayland compositor inspired with openbox.  I've got no complaints.
```
sudo apt install labwc
```
### 2. Setup labwc
- Creat a PAM configuration file for your specific user
PAM manages permissions for services and allows labwc to start with automatic login
```
sudo nano /etc/pam.d/YOUR_USERNAME
```
- Paste in the following:
```
auth    required    pam_unix.so nullok
account required    pam_unix.so
session required    pam_unix.so
session required    pam_systemd.so
```
- Setup autostart
```
sudo nano ~/.config/labwc/autostart
```
- Paste in the following:
```
/usr/bin/firefox \
  -url http://192.168.69.21:8123 &
```
