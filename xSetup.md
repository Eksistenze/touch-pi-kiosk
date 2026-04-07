# Xorg
This is a kindergarten guide.  I'm gonna break down everything cause I'm tired of guides that just tell you to do stuff and you have no idea what you are doing.
### 0. Pre-setup
- Flash your SD card with Raspberry Pi OS Lite (64 bit)
  - Setup WiFi (or use ethernet), username and password, and make sure SSH is enabled
  - 32 bit might work.  Who knows?  Not me.
- Setup your pi and monitor
  - You might need a mouse and keyboard for the initial setup.  This guide assumes that you are doing everything through SSH.  But stuff updates, stuff breaks, you might have a different setup.  Having a keyboard and mouse can make troubleshooting easier.  
Basic Commands
- `sudo` - Elevates the command priviledge.  Allows you to do things that change the system that regular permissions wouldn't allow.
- `apt` - The package manager for Debian.  It will grab software from pre-configured sources and install them on your system.
- `nano` - Lightweight text editor.  To save a file, ctrl + o.  To exit, ctrl + x. To find text, ctrl + w.
  - If you are coming from Windows, this is a shift.  If I type the command `nano foo.bar`, it will create a file (if one doesn't exist) "foo" of file type "bar" and let me start typing in it.
- `cd` - Change Directory.  Basically navigates through the file system.
  - Most terminals will autocomplete, ie, if you start filling out a directory name, you can hit tab to fill in the rest.
  - The `cd` command by itself takes you back to the home directory (where you start).
  - `.` is the current directory, ie, `cd .` takes you to the directory you are in.
  - `..` is the previous directory, ie, `cd ..` takes up up a level.
  - `~` is the home directory.
- `mkdir` - Creates a directory.
- `rm` - Remove.  Pretty self explanatory.
- `ls` - List.  Gives information about directory contents.

### 1. Update and Install
A. SSH into your pi.
> I used windows + Putty.  Mac and linux have command line SSH built in.  Your pi's IP address will be displayed on the monitor or you can check your router for the IP you were assigned.
> Make the pi login automatically on startup.  This will be a kiosk.  I didn't even want to setup a login screen.  
> ```bash
> sudo raspi-config
> ```  
> System Options > Auto Login > Yes > Exit Raspberry Pi Config
> 
B. Update the pi.  The imager OS is from 2025-12-04.  Not that long ago but long enough to update some stuff.
> ```bash
> sudo apt update
> sudo apt upgrade -y
> ```
C. Install the Software
> ```
> sudo apt install --no-install-recommends xserver-xorg x11-xserver-utils openbox xinit
> ```
> This installs xorg and x11 packages with only dependencies, no extras.
> Openbox is the window manager we'll be using and we'll use xinit to start everything.
> ```
> sudo apt install firefox xscreensaver unclutter onboard -y
> ```
> or
> ```
> sudo apt install chromium xscreensaver unclutter onboard -y
> ```
> Depnding on whether you are going for Firefox or Chromium.  I honestly didn't notice that much of a difference between them.
> 
