# Setup Chromium on X11
Chromium is a bit simpler to run due to it's use of option flags.  
A. Install Chromium
> ```
> sudo apt install chromium
> ```
B. Setup script to start Chromium and halt on stop.
> If you have not done so, create a directory for your startup scripts
> ```
> sudo mkdir ~/.config/startup/
> ```
> Create the script
> ```
> sudo nano ~/.config/startup/chromium.sh
> ```
> Enter the following:
> ```
> #!/bin/bash
> 
> /usr/bin/chromium \
>   --start-maximized \
>   --no-first-run \
>   --no-default-browser-check \
>   --noerrdialogs \
>   https://YOUR.URL.HERE/
> 
> sudo /sbin/halt
> ```
> This is a **very** basic script.  
> The first part `#!` is called the ["shebang"](https://en.wikipedia.org/wiki/Shebang_%28Unix%29).  
> It tells the OS what program we are using for the script, which is the next part, `/bin/bash`, the bash shell.  
> The next part opens Chromium with the options specified.  
> `--no-first-run` Skips the annoying first run questions.  
> `--no-default-browser-check` Doesn't check if Chromium is the default browser.  
> `--noerrdialogs` Supresses error dialogs.  
> `https://YOUR.URL.HERE/` The URL that opens on start.  
> Because of how bash commands operate, that line is not finished until Chromium closes.  
> Once Chromium closes, it continues with the halt command.  
>
> Make the script executable.
> ```
> sudo chmod +x ~/config/startup/chromium.sh
> ```
> A basic safety feature on linux is that scripts can't just run.  They have to have permission.  
> This gives the file executable priviledges.
> 
C. Add Chromium to openbox autostart
> ```
> sudo nano /etc/xdg/openbox/autostart
> ```
> Add the following:
> ```
> /home/YOUR_USERNAME/.config/startup/chromium.sh &
> ```
> The `&` at the end of the command means that the process is run in the background.
> However, it's still a good idea for the browser entry to be the last one in the labwc autostart file.
#### [Back to the X11 setup](waylandSetup.md#4-on-screen-keyboard)
