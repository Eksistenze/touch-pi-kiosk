# On Screen Keyboard
For my OSK I went with [squeekboard](https://gitlab.gnome.org/World/Phosh/squeekboard).  The good folks at Raspberry Pi [forked](https://github.com/raspberrypi-ui/squeekboard) it and have it on one of their official repositories.  
I tried [Maliit Keyboard](https://github.com/maliit/keyboard) but I just could not get it to work.  
Unfortunately, squeekboard had its own set of issues.  
#### Default behavior.
- Tap a text input area on screen.
- squeekboard shows from the bottom of the screen.
- Firefox window bottom is resized to the top of squeekboard.
If this works for you, just install squeekboard like any other app and follow the directions for labwc autostart.
#### My desired behavior.
- Tap a text input area on screen.
- squeekboard shows from the bottom of the screen.
- squeekboard is drawn ***on top of*** the Firefox window.

Wayland has different "layers" that it draws on.  squeekboard, by [default](https://forums.raspberrypi.com/viewtopic.php?t=390053), is drawn on the top layer.  
For my setup, I wanted it drawn on the overlay layer.  
Additionally, squeekboard's behavior is such that is draws an exclusion zone so that windows are resized.  
These are the steps to edit the squeekboard code and compile on a raspberry pi.  
At the time I am writing this, the version is `squeekboard-1.43.1` but that can change.  
Make sure you change the commands to reflect the actual files you get.  
Additionally, there's a [bug](https://github.com/raspberrypi-ui/squeekboard/issues/20) that I've reported that keeps it from compiling and I've included the fix.  
All of this can change with an update.  If something breaks, open an issue.  

A. Change apt repositories.
> By default, the raspberry pi is setup to get software and updates from debian and raspberry pi official sources but it will not get source files, only compiled packages.  We need to change this since we are compiling from source.
> ```
> sudo nano /etc/apt/sources.list.d/raspi.sources
> ```
> The first line `Types: deb`, change it to `Types: deb deb-src`.

B. Update the package lists.
> ```
> sudo apt update
> ```
C. Create and move to a directory for our compilation.
> ```
> mkdir ~/squeekCompile && cd ~/squeekCompile
> ```
D. Get the source files.
> ```
> apt source squeekboard
> ```
> This downloads and unpacks the source files.  
> Use the `ls` command to see what was actually downloaded and what folder was created.  
> Make sure to change the commands to reflect the version number you have.
 
E. Edit the code to change behavior.
> ```bash
> sudo nano ./squeekboard-X.XX.X/eek/layersurface.c
> ```
> a. Find the code `zwlr_layer_surface_v1_set_exclusive_zone` with `ctrl + w` and delete the entire line:
> > ```c
> > zwlr_layer_surface_v1_set_exclusive_zone (priv->layer_surface, priv->exclusive_zone);
> > ```
> > and replace with:
> > ```c
> > const char *env = getenv("SQUEEKBOARD_NO_EXCLUSION");
> > 
> > if (env && (strcmp(env, "1") == 0 || strcasecmp(env, "true") == 0)) {
> >     zwlr_layer_surface_v1_set_exclusive_zone(priv->layer_surface, -1);
> > } else {
> >     zwlr_layer_surface_v1_set_exclusive_zone(priv->layer_surface,
> >                                              priv->exclusive_zone);
> > }
> > ```
> > The line you replaced sets the exclusion zone.  
> > The code you entered checks for a variable, `SQUEEKBOARD_NO_EXCLUSION=1`, that we can use when starting the app.  
> > If the variable is present, it sets the exclusion zone to -1 (or nothing).  
> > If the variable is not present, it sets the exclusion zone as normal.
> 
> b. Find the code `phosh_layer_surface_set_layer` with `ctrl + w`.
> > This **should** take you to a comment above the function we are working with.
> > Inside the function that looks like this:
> > ```c
> > void
> > phosh_layer_surface_set_layer (PhoshLayerSurface *self, guint32 layer)
> > {
> >   PhoshLayerSurfacePrivate *priv;
> > 
> >   g_return_if_fail (PHOSH_IS_LAYER_SURFACE (self));
> >   priv = phosh_layer_surface_get_instance_private (self);
> > ```
> > Add the following code directly after the `priv =` line:
> > ```c
> >   const char *env = g_getenv("SQUEEKBOARD_LAYER");
> >   if (env && g_strcmp0(env, "overlay") == 0) {
> >     layer = ZWLR_LAYER_SHELL_V1_LAYER_OVERLAY;
> >   }
> > ```
> > This code checks for the variable, `SQUEEKBOARD_LAYER=overlay`, that, if present, changes the layer squeekboard is drawn on.
> > Specifically, it change it to `ZWLR_LAYER_SHELL_V1_LAYER_OVERLAY`.
>
> d. Fix a [bug](https://github.com/raspberrypi-ui/squeekboard/issues/10) that will stop the build from compiling.
> > ```bash
> > sudo nano ./squeekboard-X.XX.X/src/state.rs
> > ```
> > Find `fn scaling_test_wide(pixel_width:` and scroll down.
> > Add the following after `scale,`
> > ```rust
> >                 name: None,
> > ```
> > Should look like this:
> > ```rust
> >         assert_eq!(
> >             Application::get_preferred_height_and_arrangement(&OutputState {
> >                 current_mode: Some(Mode {
> >                     width: pixel_width,
> >                     height: pixel_height,
> >                 }),
> >                 geometry: Some(Geometry{
> >                     transform: c::Transform::Normal,
> >                     phys_size: Size {
> >                         width: Some(Millimeter(physical_width)),
> >                         height: Some(Millimeter(physical_height)),
> >                     },
> >                 }),
> >                 scale,
> >                 name: None,
> >             }),
> > ```
F. Get software to compile and that squeekboard requires to run.
> ```
> sudo apt install devscripts equivs gnome-themes-extra-data
> ```
G. Build a package with all the build dependencies and install them.
> Normally, when you install an something using apt, it will install everything that you need, called dependencies.
> Since we aren't installing using apt, we need to install those ourselves.
> That's what this command does.
> ```
> sudo mk-build-deps -i ./squeekboard-X.XX.X/debian/control
> ```
H. Move into the directory and compile.
> This is going to create an installable package from the source code.
> The `-uc -us` options tell it not to sign it.  Since this is a local installation and we aren't publishing it anywhere, there's no need for a cryptographic signature.
> ```
> cd squeekboard-X.XX.X/
> sudo dpkg-buildpackage -b -uc -us
> ```
I. Install the package
> ```
> sudo dpkg -i ./squeekboard_X.XX.X-1+rpt1_arm64.deb
> ```
J. Change system setting to make squeekboard work.
> This enables the on screen keyboard accessibility setting.
> ```
> gsettings set org.gnome.desktop.a11y.applications screen-keyboard-enabled true
> ```
K. Finally, enable squeezeboard to start with labwc.
> ```
> sudo nano ~/.config/labwc/autostart
> ```
> Add this to the autostart script.
> ```
> SQUEEKBOARD_NO_EXCLUSION=1 SQUEEKBOARD_LAYER=overlay /usr/bin/squeekboard &
> ```
L. If you want, you can now delete the directory we used for all this with:
> ```
> sudo rm -rf ~/squeekCompile
> ```
### [Back to the guide](waylandSetup.md#4-on-screen-keyboard)
