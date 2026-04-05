# touch-pi-kiosk
## A step by step guide to setting up a Raspberry pi as a touch kiosk for Home Assistant and more
If you want to get right to the guides the links are below.  If you want to read me blather on, I guess continue reading.

[Wayland Setup](waylandSetup.md)

[X/Xorg/X11 Setup](waylandSetup.md)

---

### Hardware
This is what I used.  I make no guarentee that it will work with anything else.  That being said, it should.  I'm not doing anything crazy.
- Raspberry Pi 4 8GB
- Dell P2418HT Touch Screen Monitor
  - Linux has pretty good support for touchscreens.  If you've got something odd, make sure it works.
- An SD card.  Get a decent one.

I also have a speaker plugged into the aux port.  I use a UE Boom 2.  I didn't want to deal with bluetooth.  There are many guides how to setup audio.  I just wanted something that could play music in my kitchen.  I go over my setup for this in the extras.

---

### Why?
I have Homa Assistant set up and I wanted a kiosk for that.  I talked to my family about what else they would want and they came up with this list.
- Recipes
- Youtube
- Music
- Slideshow of picture when idle
- On screen keyboard
- For me, all of this but without giving my info to Google or Amazon.

---

### Wayland or X???
Since I was rocking a Raspberry Pi, I wanted to keep the setup as *LEEEEAN* as possible.  No desktop.  Just boot up into a webpage and everything works.
I did full tests with X and Wayland to see which was faster.  I also tested Chromium vs Firefox on each.

### [What I Went With (Wayland)](waylandSetup.md)
- Wayland + labwc
  - I really wanted to go with Cage-Kiosk.  It felt super responsive.  Unfortunately, it doesn't yet support OSKs. See [cage #345](https://github.com/cage-kiosk/cage/pull/345)
  - When it does, I may revisit this.
- Firefox/Chromium (browser)
- Squeekboard (OSK)
- Swayidle + ImmichFrame (screensaver)
- Squeezelite (Music)

### [What I Didn't Go With (X) But Have Guides For](xSetup.md)
 - X11/Xorg + openbox
 - Firefox/Chromium (browser)
 - onboard (OSK)
 - xScreensaver + webscreensaver (screensaver)
 - Squeezelite (Music)
