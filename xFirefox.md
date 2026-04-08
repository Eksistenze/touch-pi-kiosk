# Setup Firefox on X11
A. Ensure Firefox is installed.
> If you are following the guide, you should have alread done that but, if not, this is the commmand.
> ```
> sudo apt install firefox
> ```
> I want Firefox to just run.  No pop ups.  No ads.  Just start up and there is the site.
> To do this we are going to configure some preferences, but first we need to make a profile.
> 
B. Firefox is weird with their use of profiles so we are going to do this.
> ```
> firefox -CreateProfile YOUR_PROFILE_NAME_HERE --headless --screenshot /dev/null
> ```
> This does a few things:  
> - `firefox` Starts Firefox
> - `--CreateProfile` Creates the profile with the name you specified
> - `--headless` Runs headless, as in Firefox doesn't display anything
> - `--screenshot` Forces Firefox to fully start, as apposed to just creating the profile, which we want.
> - `/dev/null` Send that screenshot and any output straight to nowhere, since we don't want it.

C. Move to the directory where firefox stores information and show the contents.
> ```
> cd ~/.config/mozilla/firefox/
> ls
> ```
> You are looking for the profile you just created.  
> Firefox adds an eight character prefix to profile folders  
> XXXXXXXX.YOUR_PROFILE_NAME
> 
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
E. Setup script for halt on close.
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
> 
F. Start Firefox on boot.
> ```
> sudo nano ~/.config/labwc/autostart
> ```
> Add the following:
> ```
> /home/YOUR_USERNAME/.config/startup/firefox.sh &
> ```
> The `&` at the end of the command means that the process is run in the background.
> However, it's still a good idea for the browser entry to be the last one in the labwc autostart file.
