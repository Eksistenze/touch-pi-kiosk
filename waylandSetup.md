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
#### If you want to stop and reboot here to make sure it works, the command for that is ```sudo reboot```.  The only thing you should see is a blank screen with a mouse pointer, since we haven't setup any applications.  If not, just keep reading.
### 3. Setup Browser
I tested both Chromium and Firefox.  Chromium runs noticably better but I went with Firefox.
- One, I support Firefox as an alternative to Chromium based browsers.
- And two, I can install uBlock Origin and block ads.
If you want to use Chromium, I've included instructions.  
[Chromium on Wayland](waylandChromium.md)  

A. Install Firefox
> ```
> sudo apt install firefox
> ```
> I want Firefox to just run.  No pop ups.  No ads.  Just start up and there is the site.
> To do this we are going to configure some preferences, but first we need to make a profile.
B. Firefox is weird with their use of profiles so we are going to do this.
> ```
> firefox -CreateProfile YOUR_PROFILE_NAME_HERE --headless --screenshot /dev/null
> ```
> This does a few things:  
- `firefox` Starts Firefox
- `--CreateProfile` Creates the profile with the name you specified
- `--headless` Runs headless, as in Firefox doesn't display anything
- `--screenshot` Forces Firefox to fully start, as apposed to just creating the profile, which we want.
- `/dev/null` Send that screenshot and any output straight to nowhere, since we don't want it.

C. Move to the directory where firefox stores information and show the contents.
> ```
> cd ~/.config/mozilla/firefox/
> ls
> ```
> You are looking for the profile you just created.  
> Firefox adds an eight character prefix to profile folders  
> XXXXXXXX.YOUR_PROFILE_NAME  
D. Then move into that folder and start writing in a file called "user.js"
> ```
> cd /XXXXXXXX.YOUR_PROFILE_NAME
> nano user.js
> ```
> I put a BUNCH of stuff in here.  I break everything down [here](firefox.md).
> ```js
> user_pref("browser.sessionstore.resume_from_crash", false);
> user_pref("browser.ai.control.default", "blocked");
> user_pref("browser.ai.control.linkPreviewKeyPoints", "blocked");
> user_pref("browser.ai.control.pdfjsAltText", "blocked");
> user_pref("browser.ai.control.sidebarChatbot", "blocked");
> user_pref("browser.ai.control.smartTabGroups", "blocked");
> user_pref("browser.ai.control.translations", "blocked");
> user_pref("browser.newtabpage.activity-stream.feeds.section.topstories", false);
> user_pref("browser.newtabpage.activity-stream.showSponsored", false);
> user_pref("browser.newtabpage.activity-stream.showSponsoredCheckboxes", false);
> user_pref("browser.newtabpage.activity-stream.showSponsoredTopSites", false);
> user_pref("browser.newtabpage.disableNewTabAsAddon", true);
> user_pref("browser.profiles.enabled", false);
> user_pref("browser.shell.checkDefaultBrowser", false);
> user_pref("browser.shell.defaultBrowserCheckCount", 1);
> user_pref("browser.shell.didSkipDefaultBrowserCheckOnFirstRun", true);
> user_pref("browser.tabs.groups.smart.enabled", false);
> user_pref("browser.tabs.groups.smart.userEnabled", false);
> user_pref("datareporting.healthreport.uploadEnabled", false);
> user_pref("datareporting.policy.firstRunURL", "");
> user_pref("datareporting.usage.uploadEnabled", false);
> user_pref("extensions.formautofill.addresses.enabled", false);
> user_pref("extensions.formautofill.creditCards.enabled", false);
> user_pref("signon.rememberSignons", false);
> user_pref("trailhead.firstrun.didSeeAboutWelcome", true);
> ```
D. Make Firefox run maximized.
> Firefox has a kiosk mode that you can activate with the option `-kiosk` when launching.  
> I didn't want that, nor did I want to run full screen.  
>
> Create `rc.xml`
> ```
> sudo nano ~/.config/labwc/rc.xml
> ```
> This is a file that changes the way labwc behaves.  You can change themes, set keybind and do all sorts of things with the config files.
> Put this in there.
> ```
> <?xml version="1.0"?>
> <labwc_config>
> 
>   <windowRules>
>     <windowRule identifier="firefox">
>       <action name="Maximize"/>
>     </windowRule>
>   </windowRules>
> 
> </labwc_config>
> ```
> xml files have specific formatting requirements so make sure you know what you are doing if you edit them.  
> This creates a window rule that affects "firefox" and runs the action Maximize.
F. Setup script for halt on close.
> Create a folder to hold your startup scripts.
> ```
> sudo mkdir ~/.config/startup/
> ```
> Create Firefox script.
> ```
> sudo nano ~/.config/startup/firefox.sh
> ```
> Enter the following:
> ```
> #!/bin/bash
> 
> /usr/bin/firefox \
>   -p YOUR_PROFILE_NAME_HERE
>   -url https://YOUR.URL.HERE/
> 
> sudo /sbin/halt
> ```
> This is a **very** basic script.  
> The first part `#!` is called the ["shebang"](https://en.wikipedia.org/wiki/Shebang_%28Unix%29).  It tells the OS what program we are using for the script, which is the next part, `/bin/bash`, the bash shell.  
> The next part opens Firefox with the options specified.  
> Because of how bash commands operate, that line is not finished until Firefox closes.  
> Once Firefox closes, it continues with the halt command.
>
> Make the script executable.
> ```
> sudo chmod +x ~/config/startup/firefox.sh
> ```
> A basic safety feature on linux is that scripts can't just run.  They have to have permission.
> This gives the file executable priviledges.
G. Start Firefox on boot.
> ```
> sudo nano ~/.config/labwc/autostart
> ```
> Add the following:
> ```
> /home/YOUR_USERNAME/.config/startup/firefox.sh &
> ```
> The `&` at the end of the command means that the process is run in the background.
> However, it's still a good idea for the browser entry to be the last one in the labwc autostart file.
#### Firefox is now setup and **should** start on boot, maximized, with no prompts or popups.
### 4. On Screen Keyboard
The instructions for squeekboard, the on screen keyboard I went with, turned out to be pretty long and involved so I've separated it into it's only file.
#### [squeekboard Guide](squeekboard.md)
### 5. Screensaver
This part is a bit hacky.  Wayland, and linux in general, have decided that there is no need for screensavers anymore.  
I understand that there is no need.  I don't need one.  But I want it.  
I want a screensaver that displays a slideshow of pictures.  
Fortunately, I am running Immich on unraid so I already have my photos on my network.  
You need to setup [ImmichFrame](https://immichframe.dev/) with [Immich](https://immich.app/) for this to work.  
That setup is outside of the scope of this guide.  If you want something simpler, lookup [swayimg](https://github.com/artemsen/swayimg) or [swaybg](https://github.com/swaywm/swaybg)
A. Install the software.
> ```
> sudo apt install swayidle
> ```
> swayidle is an app that does things after your machine has been idle.  You can set custom commands and specify time as well as what happens after activity.
> ```
> cd
> sudo wget https://github.com/immichFrame/ImmichFrame_Desktop/releases/download/v0.1.0/immichframe_0.1.0_LINUX_arm64.deb
> sudo apt install ./immichframe_0.1.0_LINUX_arm64.deb
> ```
> `cd` moves you to the home directory.
> `wget` downloads the ImmichFrame Desktop linux client from their respository.  
> `apt` installs the package we just downloaded.
B. Setup swayidle
> ```
> sudo nano ~/.config/labwc/autostart
> ```
> Add the following:
> ```
> /usr/bin/swayidle -w \
>   timeout 600 'GDK_BACKEND=wayland GDK_GL=gles immichframe' \
>   resume 'pkill immichframe' &
> ```
> This adds the swayidle binary to labwc autostart.  
> The `-w` wait option means that it waits for immichFrame to finish before it continues.  
> Behavior:
> > ImmichFrame has areas on screen that allow for pausing, next and previous picture, and exit.  
> > To exit the screensaver, tap towards the bottom of the screen.
> Behavior without `-w`:
> > Any touch on the screen kills the process.
> `timeout 600` The amount of seconds before swayidle performs the action.
> `GDK_BACKEND=wayland GDK_GL=gles` Variables to make immichFrame run better in Wayland.
> `resume 'pkill immichframe'` What to do on activity, which is kill the immichFrame process.
C. Setup immichFrame
> Ensure the immichFrame folder has been created.
> ```
> sudo mkdir ~/.config/immichFrame/
> ```
> Create and edit settings file.
> ```
> sudo nano ~/.config/immichFrame/Settings.txt
> ```
> Enter the following:
> ```
> https://YOUR.IMMICHFRAME.URL/
> ```
#### Screensaver is now setup.
### 6. Audio (squeezlite)
As the steps for 
