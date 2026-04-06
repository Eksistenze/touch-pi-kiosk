# Squeezelite Setup
A long time ago, Logitech had a line of media player boxes called Squeezbox and software called Logitech Media Server to deliver music.  
Along the line, [someone else](https://code.google.com/archive/p/squeezelite/) wrote a software called squeezelite, basically a Squeezebox emulator.
This software could be installed on a computer and music streamed from a server.  It is now developed [here](https://github.com/ralph-irving/squeezelite).

### My setup
I just have a speaker plugged into my pi using an aux cable.  This was enough for my use case.
There are a [ton](https://www.hifiberry.com/) of [great](https://allo.com/index.html) [options](https://www.audiophonics.fr/en/raspberry-pi-and-rpi-modules-c-660.html)
if you want really good audio.  I just wanted a kitchen speaker.
#### Note: This is just the player.  You need a music server setup.  You can download [Lyrion Music Player](https://lyrion.org/) or run a [Home Assistant](https://www.home-assistant.io/) somewhere and use [Music Assistant](https://www.music-assistant.io/).

1. Install the software.
> ```
> sudo apt install squeezelite alsa-utils
> ```
> alsa is the sound framework for linux and alsa-utils provides some tools.
2. Setup squeezelite
> ```
> squeezelite -l
> ```
> This command gives you a list of available sound outputs.  I'm using the one called `plughw:CARD=Headphones,DEV=0`.  
> If you want to make sure your output works, you can use the command:
> ```
> speaker-test -t sine -f 440 -c 2 -l 2 -D YOUR_HARDWARE_NAME
> ```
> `speaker-test` Comes with alsa-utils.  
> `-t sine` Uses a sin wave for testing.  
> `-f 440` Indicates the frequency.  
> `-c 2` 2 channels.  
> `-l 2` Loop twice.  
> `-D YOUR_HARDWARE_NAME` The device name you are testing.  
> ```
> sudo nano ~/.config/squeezelite.conf
> ```
> Enter the following (adjust for your settings):
> ```
> SQUEEZELITE_OPTIONS="
> -n PLAYER_NAME
> -o YOUR_HARDWARE_NAME
> -s https://YOUR.SERVER.URL/
> "
> ```
3. Starting up squeezelite
> You can start squeezelite using the labwc autostart file but I don't really like to.  
> labwc has some limitations, by design, and doesn't impliment any kind of restart on crash.  
> So I like to setup squeezelite as a systemd service.
> ```
> sudo rm -f /etc/init.d/squeezelite
> ```
> Remove the init.d service script that comes with squeezelite.
>
> Create the systemd service.
> ```
> sudo nano /etc/systemd/system/squeezelite.service
> ```
> Enter the following:
> ```
> [Unit]
> Description=Squeezelite Player
> After=sound.target network-online.target
> 
> [Service]
> Type=simple
> EnvironmentFile=~/.config/squeezelite.conf
> ExecStart=/usr/bin/squeezelite $SQUEEZELITE_OPTIONS
> Restart=on-failure
> RestartSec=5
> 
> [Install]
> WantedBy=multi-user.target
> ```
> Breakdown of this script.
> `[Unit]` Give basic information about the service and when it can run, namely after sound and network have started.  
> `[Service]` What this service does.  It is a simple service.  
> `EnvironmentFile` Pulls in the file we created earlier.  
> `ExecStart` What to do on start, namely start the app with the config we created.  
> `Restart` This is why I like to do this.  Is squeezelite fails, the system will automatically try to restart the service.  
> `[Install]` Tells systemctl what do do when we enable this service.  
> `WantedBy=multi-user.target` Creates a link between startup and this service so when the pi starts up, it tries to start this as well.  
4. Enabling squeezelite
> ```
> sudo systemctl enable squeezelite
> ```
### Squeezelite is Setup!
