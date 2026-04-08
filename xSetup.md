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
> sudo apt install firefox xscreensaver unclutter-xfixes onboard -y
> ```
> or
> ```
> sudo apt install chromium xscreensaver unclutter-xfixes onboard -y
> ```
> Depnding on whether you are going for Firefox or Chromium.  I honestly didn't notice that much of a difference between them.
D. Setup openbox startup
> ```
> sudo nano ~/.profile
> ```
> This file is already setup on your raspberry pi.  
> Just add this to the end.
> ```
> [[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec xinit /usr/bin/openbox-session -- vt$(fgconsole)
> ```
> `-z $DISPLAY` Check if that variable is empty, ie, there is no display currently running.  
> `$XDG_VTNR -eq 1` Checks if this is TTY1, or, the terminal you log into (auto log into) when starting your pi.  
> `xinit` The tool that starts the window manager.  
> `/usr/bin/openbox-session` Location of what xinit is running.  
> `vt$(fgconsole)` Tells xinit to start on the current virtual console.  
>
> Next went want to setup the openbox autostart file.  
> This is the file that we put everything that openbox needs to start.
> ```
> sudo nano /etc/xdg/openbox/autostart
> ```
> Put this inside:
> ```
> xset -dpms
> xset s noblank
> xset s off
> ```
> These turn off the energy star features, turn off screen blanking and turn off the screensaver.  We want to control all of that ourselves.
E. Setup browser.
> I've splite the browser into two setups, depending on what you go with.  
> [Chromium](xChromium.md) and [Firefox](xFirefox.md)
> 
> If you wanna stop here and setup your own stuff, the pi should now startup with your browser.
> 

> squeezelite -n $PLAYER_NAME -o $SOUND_DEVICE -s $SERVER_IP &
> unclutter-xfixes --timeout 8 --hide-on-touch &
> /home/eksistenze/.config/startup/firefox.sh &
> xscreensaver -no-splash &
> onboard &
-------------------------------------------------
sudo mkdir ~/.config/startup/
sudo nano ~/.config/startup/firefox.sh

#!/bin/bash

/usr/bin/firefox \
  -p YOUR_PROFILE_NAME_HERE
  -url https://YOUR.URL.HERE/

sudo /sbin/halt

sudo chmod +x ~/.config/startup/firefox.sh
-------------------------------------------------
sudo nano /etc/xdg/openbox/environment

### Browser Homepage
export TOUCH_URL=http://192.168.69.21:8123
-------------------------------------------------
mkdir ~/.config/openbox/
cp /etc/xdg/openbox/rc.xml ~/.config/openbox/
sudo nano ~/.config/openbox/rc.xml
# Find </applications>
***Last section in the file
<application role="browser">
  <maximized>yes</maximized>
</application>
-------------------------------------------------


sudo nano ~/.profile
[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && exec xinit /usr/bin/openbox-session -- vt$(fgconsole)
###[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && startx
####For now.  Final will be
####[[ -z $DISPLAY && $XDG_VTNR -eq 1 ]] && startx -- -nocursor

-------------------------------------------------
# enables onboard keyboard show when text box selected
dconf write /org/onboard/auto-show/enabled true
# "docks" onboard on bottom of the screen, not visible
dconf write /org/onboard/window/docking-enabled true
# don't resize window when onboard is shown, instead it is shown on top
dconf write /org/onboard/window/docking-shrink-workarea false
# enable Gnome accessibility(required for correct onboard behavior)
dconf write /org/gnome/desktop/interface/toolkit-accessibility true
-------------------------------------------------
firefox -CreateProfile eksistenze --headless --screenshot /dev/null
cd ~/.config/mozilla/firefox/
ls
# you are looking for the profile you just created.
# Firefox adds an eight character prefix to profile folders
# XXXXXXXX.PROFILE_NAME
cd /XXXXXXXX.PROFILE_NAME
nano user.js
-------------------------------------------------
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
-------------------------------------------------
sudo apt install -y squeezelite alsa-utils

# Setup conf file
# This gives a list of outputs.  You can read more about what each of these does to fit your particular setup.
# For me, I just have a speaker plugged into my pi so I am using plughw:CARD=Headphones,DEV=0
squeezelite -l
sudo nano .config/squeezelite.conf

SQUEEZELITE_OPTIONS="
-n TouchSqueeze
-o plughw:CARD=Headphones,DEV=0
-s http://192.168.69.21
"

# We are gonna pull squeezelite into the 2020s and start it as a systemd service.
sudo rm -f /etc/init.d/squeezelite
sudo nano /etc/systemd/system/squeezelite.service

[Unit]
Description=Squeezelite Player
After=sound.target network-online.target

[Service]
Type=simple
EnvironmentFile=~/.config/squeezelite.conf
ExecStart=/usr/bin/squeezelite $SQUEEZELITE_OPTIONS
Restart=on-failure
RestartSec=5

[Install]
WantedBy=multi-user.target

sudo systemctl enable squeezelite
-------------------------------------------------
sudo apt install python3 python3-gi gir1.2-webkit2-4.1 gir1.2-gtk-3.0
wget https://raw.githubusercontent.com/lmartinking/webscreensaver/master/webscreensaver
sudo mv webscreensaver /usr/libexec/xscreensaver
sudo chmod +x /usr/libexec/xscreensaver/webscreensaver
cp ~/.xscreensaver ~/.xscreensaver.original
sudo nano .xscreensaver
alt + /
ctrl + 6
alt + \
alt + t
# XScreenSaver Preferences File
# Written by xscreensaver-demo 6.09 for eksistenze on Mon Apr  6 22:14:29 2026.
# https://www.jwz.org/xscreensaver/

timeout:        0:10:00
cycle:          0:10:00
lock:           False
lockTimeout:    0:00:00
passwdTimeout:  0:00:30
visualID:       default
installColormap:    True
verbose:        False
splash:         True
splashDuration: 0:00:05
demoCommand:    xscreensaver-settings
nice:           10
fade:           True
unfade:         True
fadeSeconds:    0:00:03
ignoreUninstalledPrograms:False
dpmsEnabled:    False
dpmsQuickOff:   False
dpmsStandby:    2:00:00
dpmsSuspend:    2:00:00
dpmsOff:        4:00:00
grabDesktopImages:  False
grabVideoFrames:    False
chooseRandomImages: False
imageDirectory:

mode:           one
selected:       0

textMode:       url
textLiteral:    XScreenSaver
textFile:
textProgram:    fortune
textURL:        https://planet.debian.org/rss20.xml
dialogTheme:    default
settingsGeom:   259,154 1238,154

programs:                                                                     \
                                webscreensaver -url                           \
                                  http://192.168.69.246:8081/share/IRFVnvi4SmhA>


pointerHysteresis:  10
authWarningSlack:   20
