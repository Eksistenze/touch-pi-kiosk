# Firefox ```user.js``` Breakdown
Firefox tracks all profile preferences and saves them locally at
```
~/.config/mozilla/firefox/XXXXXXXX.YOUR_PROFILE_NAME/
```
It saves them to a file called `prefs.js`.  But since firefox uses that file, it doesn't preserve changes you make to it.
Instead, it looks for a file called `user.js` and uses any values in there to override settings in `prefs.js`.
There are a [ton](https://github.com/yokoffing/Betterfox) you can [find](https://github.com/arkenfox/user.js/) online to customize your firefox experience.  Here's mine and what everything does:
> Don't resume from crash.
> In my setup, there is no graceful shutdown.  Anytime I turn off the pi, everything just stops.  This gets rid of that annoying "Resume from crash" option when I start up again.
```
user_pref("browser.sessionstore.resume_from_crash", false);
```
> NO AI!
```
user_pref("browser.ai.control.default", "blocked");
user_pref("browser.ai.control.linkPreviewKeyPoints", "blocked");
user_pref("browser.ai.control.pdfjsAltText", "blocked");
user_pref("browser.ai.control.sidebarChatbot", "blocked");
user_pref("browser.ai.control.smartTabGroups", "blocked");
user_pref("browser.ai.control.translations", "blocked");
```
> I don't want firefox offering me suggested pages to go to.  I don't want ads.
```
user_pref("browser.newtabpage.activity-stream.feeds.section.topstories", false);
user_pref("browser.newtabpage.activity-stream.showSponsored", false);
user_pref("browser.newtabpage.activity-stream.showSponsoredCheckboxes", false);
user_pref("browser.newtabpage.activity-stream.showSponsoredTopSites", false);
user_pref("browser.newtabpage.disableNewTabAsAddon", true);
```
> I've created the only profile that will run on my machine.  No need for more.
```
user_pref("browser.profiles.enabled", false);
```
> No questions about default browser.
```
user_pref("browser.shell.checkDefaultBrowser", false);
user_pref("browser.shell.defaultBrowserCheckCount", 1);
user_pref("browser.shell.didSkipDefaultBrowserCheckOnFirstRun", true);
```
> More AI bullshit.
```
user_pref("browser.tabs.groups.smart.enabled", false);
user_pref("browser.tabs.groups.smart.userEnabled", false);
```
> No data reporting to Mozilla.
```
user_pref("datareporting.healthreport.uploadEnabled", false);
user_pref("datareporting.policy.firstRunURL", "");
user_pref("datareporting.usage.uploadEnabled", false);
```
> No popups to remember credit cards, passwords, addresses, etc.
```
user_pref("extensions.formautofill.addresses.enabled", false);
user_pref("extensions.formautofill.creditCards.enabled", false);
user_pref("signon.rememberSignons", false);
```
> No welcome for first run or, hopefully, any other run if I update.
```
user_pref("trailhead.firstrun.didSeeAboutWelcome", true);
```
