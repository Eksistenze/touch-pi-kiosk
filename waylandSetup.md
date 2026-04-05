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
sudo nano /etc/pam.d/YOUR_USERNAME_HERE
```
- Paste in the following:
```
auth    required    pam_unix.so nullok
account required    pam_unix.so
session required    pam_unix.so
session required    pam_systemd.so
```
> Setup labwc autostart
> This the startup script that labwc uses.  It does not start labwc itself.
```
sudo nano ~/.config/labwc/autostart
```
> Paste in the following:
```
/usr/bin/firefox \
  -url https://YOUR_URL_HERE &
```
- Setup labwc start on login
> This is a script that is run on login.
> Since we have autologin setup, it will run at startup.
```
sudo nano ~/.bash_profile
```
> Paste in the following:
> This checks if the tty is tty1 and that there is no Wayland instance running.
> If both are true, it starts a dbus session with labwc.
```
# Start labwc only on tty1
if [ "$(tty)" = "/dev/tty1" ] && [ -z "$WAYLAND_DISPLAY" ]; then
  exec dbus-run-session labwc
fi
```
#### If you want to stop and reboot here to make sure it works, the command for that is ```sudo reboot```.  If not, just keep reading.
### 3. Setup Firefox
- Firefox is weird with their use of profiles so we are going to do this.
```
firefox -CreateProfile YOUR_PROFILE_NAME_HERE --headless --screenshot /dev/null
```
> This does a few things:  
- `firefox` Starts Firefox
- `--CreateProfile` Creates the profile with the name you specified
- `--headless` Runs headless, as in Firefox doesn't display anything
- `--screenshot` Forces Firefox to fully start, as apposed to just creating the profile, which we want.
- `/dev/null` Send that screenshot and any output straight to nowhere, since we don't want it.

- Move to the directory where firefox stores information and show the contents.
```
cd ~/.config/mozilla/firefox/
ls
```
You are looking for the profile you just created.  
Firefox adds an eight character prefix to profile folders  
XXXXXXXX.YOUR_PROFILE_NAME  
Then move into that folder and start writing in a file called "user.js"
```
cd /XXXXXXXX.YOUR_PROFILE_NAME
nano user.js
```
I put a BUNCH of stuff in here.  I break everything down [here](firefox.md).
```
user_pref("browser.sessionstore.resume_from_crash", false);
user_pref("browser.ai.control.default", "blocked");
user_pref("browser.ai.control.linkPreviewKeyPoints", "blocked");
user_pref("browser.ai.control.pdfjsAltText", "blocked");
user_pref("browser.ai.control.sidebarChatbot", "blocked");
user_pref("browser.ai.control.smartTabGroups", "blocked");
user_pref("browser.ai.control.translations", "blocked");
user_pref("browser.newtabpage.activity-stream.feeds.section.topstories", false);
user_pref("browser.newtabpage.activity-stream.showSponsored", false);
user_pref("browser.newtabpage.activity-stream.showSponsoredCheckboxes", false);
user_pref("browser.newtabpage.activity-stream.showSponsoredTopSites", false);
user_pref("browser.newtabpage.disableNewTabAsAddon", true);
user_pref("browser.profiles.enabled", false);
user_pref("browser.shell.checkDefaultBrowser", false);
user_pref("browser.shell.defaultBrowserCheckCount", 1);
user_pref("browser.shell.didSkipDefaultBrowserCheckOnFirstRun", true);
user_pref("browser.tabs.groups.smart.enabled", false);
user_pref("browser.tabs.groups.smart.userEnabled", false);
user_pref("datareporting.healthreport.uploadEnabled", false);
user_pref("datareporting.policy.firstRunURL", "");
user_pref("datareporting.usage.uploadEnabled", false);
user_pref("extensions.formautofill.addresses.enabled", false);
user_pref("extensions.formautofill.creditCards.enabled", false);
user_pref("signon.rememberSignons", false);
user_pref("trailhead.firstrun.didSeeAboutWelcome", true);
```
