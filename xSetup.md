# Xorg
This is a kindergarten guide.  I'm gonna break down everything cause I'm tired of guides that just tell you to do stuff and doesn't explain what you are doing.
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
> 
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
> unclutter-xfixes --timeout 8 --hide-on-touch &
> ```
> These turn off the energy star features, turn off screen blanking and turn off the screensaver.  We want to control all of that ourselves.
> It also starts `unclutter-xfixes`.  This is a utility that hides the mouse pointer and it good for touchscreens.
### 2. Setup browser.
> I've splite the browser into two setups, depending on what you go with.  
> [Chromium](xChromium.md) and [Firefox](xFirefox.md)  
> Regardless of browser, we can use the openbox configuration to make the window maximized.  There are other options, if you are looking for a true kiosk mode.  I wanted access to tabs.
> Openbox configuration can create keybinds, change behavior and themes.
> 
A. Create the director for your configuration.
> ```
> mkdir ~/.config/openbox/
> ```
> Openbox checks here for configuration and, if it finds one, it overrides the default.
> 
B. Copy the default configuration file so we can edit it.
> ```
> cp /etc/xdg/openbox/rc.xml ~/.config/openbox/
> ```
C. Open the `rc.xml` file we just copied.
> ```
> sudo nano ~/.config/openbox/rc.xml
> ```
D. Find the text `</applications>` using `ctrl + w` and put the following just above that line.
> ```
> <application role="browser">
>   <maximized>yes</maximized>
> </application>
> ```
If you wanna stop here and setup your own stuff, the pi should now startup with your browser.
### 3. On Screen Keyboard
> If you are following the guide, you've already installed `onboard` so we just have to configure a few settings to make it work.
> ```
> dconf write /org/onboard/auto-show/enabled true
> dconf write /org/onboard/window/docking-enabled true
> dconf write /org/onboard/window/docking-shrink-workarea false
> dconf write /org/gnome/desktop/interface/toolkit-accessibility true
> ```
> In order, these:  
> a. Enable automatic show/hide when selecting a text area.  
> b. Enable docking the keyboard at the bottom of the screen.  
> c. Disable window shrinking, ie, the keyboard is on top of the browser window.  
> d. Enable accessibility options to make the keyboard work at all.
> Open your autostart file:
> ```
> sudo nano /etc/xdg/openbox/autostart
> ```
> And add `onboard` to it:
> ```
> onboard &
> ```
> Once these are configured, the keyboard should work.  You may have to restart.   
> I also changed the theme for the keyboard but you can do that with the GUI.
### 4. Screensaver
`xscreensaver` is a wonderful program, first released in 1992!  
While it has some EXCELLENT options that you should feel free to explore, I wanted a slideshow of pictures.  
Google already has this functionality with it's Hub devices and I wanted to replicate that.  
Fortunately, I already host Immich for my photos.  I setup Immich Kiosk as well, which generates a website that is just a slideshow of pictures you've selected.  
Setting all that up is outside this guide's scope.  There are some great guides out there.  
> `xscreensaver` should already be installed if you are using this guide.  
> We are going to use [webscreensaver](https://github.com/lmartinking/webscreensaver)  
> a. Install the dependencies for the screensaver.
> ```
> sudo apt install python3 python3-gi gir1.2-webkit2-4.1 gir1.2-gtk-3.0
> ```
> b. Download the screensaver.
> ```
> wget https://raw.githubusercontent.com/lmartinking/webscreensaver/master/webscreensaver
> ```
> c. Move the screensaver to the directory where are the rest are kept.
> ```
> sudo mv webscreensaver /usr/libexec/xscreensaver
> ```
> d. Make it executable.
> ```
> sudo chmod +x /usr/libexec/xscreensaver/webscreensaver
> ```
> e. Add the config for `webscreensaver`.
> > ***optional*** If you already have xscreensaver setup and are following this, you probably already have a config file.
> > Check if `.screensaver` already exists:
> > ```
> > ls -a
> > ```
> > Rename that file so you can go back to it if you like.
> > ```
> > mv ~/.xscreensaver ~/.xscreensaver.original
> > ```
> Create and open a new config file.
> ```
> sudo nano .xscreensaver
> ```
> Add the following:
> ```
> # XScreenSaver Preferences File
> # Written by xscreensaver-demo 6.09 for eksistenze on Mon Apr  6 22:14:29 2026.
> # https://www.jwz.org/xscreensaver/
> 
> timeout:        0:10:00
> cycle:          0:10:00
> lock:           False
> lockTimeout:    0:00:00
> passwdTimeout:  0:00:30
> visualID:       default
> installColormap:    True
> verbose:        False
> splash:         True
> splashDuration: 0:00:05
> demoCommand:    xscreensaver-settings
> nice:           10
> fade:           True
> unfade:         True
> fadeSeconds:    0:00:03
> ignoreUninstalledPrograms:False
> dpmsEnabled:    False
> dpmsQuickOff:   False
> dpmsStandby:    2:00:00
> dpmsSuspend:    2:00:00
> dpmsOff:        4:00:00
> grabDesktopImages:  False
> grabVideoFrames:    False
> chooseRandomImages: False
> imageDirectory:
> 
> mode:           one
> selected:       0
> 
> textMode:       url
> textLiteral:    XScreenSaver
> textFile:
> textProgram:    fortune
> textURL:        https://planet.debian.org/rss20.xml
> dialogTheme:    default
> settingsGeom:   259,154 1238,154
> 
> programs:                                                                     \
>                                 webscreensaver -url                           \
>                                   https://YOUR.URL.HERE
> ```
> This is just the default config, edited so that webscreensaver is the only option.  
> You can change the idle time for the screensaver in the `timeout` option.  
> f. Add `xscreensaver` to the openbox autostart.
> ```
> sudo nano /etc/xdg/openbox/autostart
> ```
> Add the following:
> ```
> xscreensaver -no-splash &
> ```
### 5. Squeezelite
Instructions for squeezelite are the same so I've put separated them.
[Squeezelite Setup](squeezelite.md)
## Success!
You now have a working kiosk.
