# dwm
Dwm is my preferred window manager and I have learned a lot over the few years using it.

In this guide, I'll explain how to install it, use it, configure it, patch it, a few tricks I've found and programs that go well with dwm.

## Installation
To install it clone the git repository of the build you want, suckless repo is the default one, but you can also have your own build on github for example.

Suckless:
```
$ git clone https://git.suckless.org/dwm
```
Own build (example):
```
$ git clone https://github.com/s3mv4/dwm
```
Then to build it:
```
$ cd dwm
```
```
$ sudo make install
```
If you get an error concerning Xft or Xinerama then you need to install these libraries:
```
$ sudo pacman -S libxft libxinerama
```
## Usage
To run it you need to append `exec dwm` to your `~/.xinitrc` and run:
```
$ startx
```
Note that you need a font in order to run it, if you dont have a font install `terminus-font`, and try again.

For the shortcuts you can look in `dwm/config.def.h` and go to the keys structure and look there.

'Mod' refers to the modkey and this is Alt by default.

Most useful ones are Mod+Shift+Return for terminal emulator, Mod+p for dmenu (to run any program) and Mod+Shift+Q to quit dwm.

## Configuration
Configuration is done in the `config.def.h` file.

Just a heads up, if you make any changes, you need to remove the `config.h` file:
```
$ rm -rf config.h
```
And build dwm again:
```
$ sudo make install
```
Most of the configuration is pretty straightforward but I will still go over a bit of the various configuration options.
___
**The first bit is for appearance.**

`borderpx` will change the thickness of the borders around the windows.

`snap` is the amount of pixels of space between the edge of the screen and the window you are dragging with your mouse for it to snap into place.

`showbar`, 1 or 0, will change if the bar is shown or not.

`topbar`, 1 or 0, will change whether the bar is at the top of the screen or at the bottom.

The `fonts` array is for the fonts that dwm will use, if it has something like an icon that cant be found in the first font it will try the next font and so on.

`dmenufont` is the font used by dmenu when its ran with the shortcut for it.

The `colors` 2d array is for the colors that dwm uses. There are 2 schemes, one for when something is selected/focused and one when its not. First column is foreground color, second is the background and third is the border color.
___
**The second bit is for tagging.**

The `tags` array is for the 'names' of each tag. By default these are just numbers increasing from 1 through 9 but you can change these into icons or something else. For example if you are always using the terminal in tag 1 then you can use a terminal icon instead of the number "1".

The `rules` array is for window rules, I don't use these at all, but you can change properties for certain windows using their class, instance or title. All of these can be acquired with xprop, just run `xprop` and click on the window you wish to know the properties of. Then you can use this information to create a rule for it.

To install xprop:
```
$ sudo pacman -S xorg-xprop
```

An example for good measure:

With Firefox open, you run `xprop` from a terminal emulator and click the Firefox window with your cursor.

The output will look something like this:
```
_NET_WM_WINDOW_TYPE(ATOM) = _NET_WM_WINDOW_TYPE_NORMAL
_NET_WM_SYNC_REQUEST_COUNTER(CARDINAL) = 4194350, 4194351
_NET_WM_USER_TIME_WINDOW(WINDOW): window id # 0x40002d
WM_CLIENT_LEADER(WINDOW): window id # 0x400001
_NET_WM_PID(CARDINAL) = 908
WM_LOCALE_NAME(STRING) = "C"
WM_CLIENT_MACHINE(STRING) = "archlinux"
WM_NORMAL_HINTS(WM_SIZE_HINTS):
                program specified minimum size: 450 by 120
                program specified maximum size: 16384 by 16384
                program specified base size: 450 by 120
                window gravity: NorthWest
WM_PROTOCOLS(ATOM): protocols  WM_DELETE_WINDOW, WM_TAKE_FOCUS, _NET_WM_PING, _NET_WM_SYNC_REQUEST
WM_CLASS(STRING) = "Navigator", "firefox"
WM_ICON_NAME(STRING) = "Mozilla Firefox"
_NET_WM_ICON_NAME(UTF8_STRING) = "Mozilla Firefox"
WM_NAME(STRING) = "Mozilla Firefox"
_NET_WM_NAME(UTF8_STRING) = "Mozilla Firefox"
```
The properties that can be used in the rules can be either class, instance, or title.

Class is by far the easiest as its bound to the program and won't change.

A program can have multiple classes in this case "Navigator" and "firefox", "Navigator" is of course way more general than "firefox" so in this case I would choose "firefox" as the class.

Then to use it in a rule:
```
{ "firefox",  NULL,       NULL,       1 << 8,       0,           -1 },
```
The `1 << 8` is a *left shift bitwise operation*, I won't explain how it works here but this one will assign Firefox to tag 9.
___
**Then we move onto layouts.**

`mfact` is the factor that the master window takes in, so it will be wider when mfact is bigger.

`nmaster` is the amount of master windows so if you set it to 2 then you will have 2 master windows stacked on top of eachother.

`resizehints`, 1 or 0, is if a window prefers to be floating or wants to have a certain size it can specify this and dwm can either respect this or not. If you set this to 0 then it will be a lot cleaner as there wont be any empty space around windows that aren't perfectly matching your resolution.

`lockfullscreen`, 1 or 0, will force the focus on the fullscreen window.

The `layouts` structure defines the layouts for dwm. There are 3 by default: tile, floating and monocle. tile is the master slave layout, monocle is all windows taking up the full size of the screen and the ones not in focus will be behind the current window. Here you can also change the text that is used for each layout similarly to the tags array. And the first entry in the struct is the default layout.
___
**Key definitions.**

The `MODKEY` can be changed, Mod1Mask is the Alt key and Mod4Mask is the Windows key (or Super key).

The `TAGKEYS` structure is for the shortcuts that involve the tags.
___
**Commands.**

First a few shorthands for complex commands like running dmenu or starting st. A custom one can be created like so:

Say the command you want a shorthand for is:
```
$ st -e top
```
(Which will launch a task manager in st)

This would be the shorthand:
```
static const char *topcmd[] = { "st", "-e", "top", NULL };
```
Notice that every space in the original command means a new entry in the array, and it always ends with NULL (also the name can be whatever of course but I chose 'topcmd' because it made sense here).

Then the `keys` structure which defines all keyboard shortcuts within dwm, I will explain all the functions used in the structure:

`spawn` can be used to run a command like the ones already defined for dmenu and st.

`togglebar` will toggle the bar between showing and hiding.

`focusstack` will shift focus through the windows on the current screen.

`incnmaster` will change the amount of master windows.

`setmfact` will change the factor of the master window.

`zoom` will adjust the layout of the windows on the current screen.

`view` will change the view from the tag to the previous tag that was used if the argument is 0. If .ui = ~0 is the argument it will change the view to all tags. 

`killclient` will kill the current window.

`setlayout` will set the layout, if the argument is 0 it will change to the previous layout that was used.

`togglefloating` will toggle a window between floating and not floating.

`tag` will move the current window to a different tag, if the argument is .ui = ~0 then it will move the current window to all tags.

`focusmon` will change the focus between multiple monitors.

`tagmon` will move the current window between monitors.
___
## Patching
Patches can be used to extend the functionality of dwm, they essentially modify bits of the source code via a diff file.

Patches can be found at [suckless.org](https://dwm.suckless.org/patches)

There are a lot and you don't need to know them all at once, I actually prefer fewer patches to keep it minimal and simple.

If you have chosen a patch you would like to add to your dwm build you can download a diff file at the download section.

Then I would suggest putting the patch file in the `dwm/patches` folder (you have to create it with mkdir).

To patch it you would run this command:
```
$ patch -p1 < path/to/patch.diff
```
With `path/to/patch.diff` obviously being the path to the diff file.

Sometimes the patch will go wrong and you will have to manually patch it with a text editor but as long as you keep it simple and don't go overboard with the amount of patches it should be easy to do and occur less frequently.
## Tricks
### Multimedia keys
You can map multimedia keys to be used in keyboard shortcuts by adding this line to your `config.def.h` (make sure its before the `keys` structure):
```
#include <X11/XF86keysym.h>
```
Then you can use `xev` to find out the name of the key you are pressing:
```
$ sudo pacman -S xorg-xev
```
```
$ xev
```
And a white window should appear, if this window is in focus and you press a key, it will show information about that key in the terminal emulator.

For example when pressing a:
```
KeyPress event, serial 32, synthetic NO, window 0xa00001,
    root 0x4dd, subw 0x0, time 4726048, (449,600), root:(2370,617),
    state 0x0, keycode 38 (keysym 0x61, a), same_screen YES,
    XLookupString gives 1 bytes: (61) "a"
    XmbLookupString gives 1 bytes: (61) "a"
    XFilterEvent returns: False
```
And the text that we are looking for here is the line with 'state' at the start and then inside the parenthesis the identifier will be after the comma.

So when pressing the volume up key:
```
KeyPress event, serial 32, synthetic NO, window 0xa00001,
    root 0x4dd, subw 0x0, time 4669592, (1065,328), root:(2986,345),
    state 0x0, keycode 123 (keysym 0x1008ff13, XF86AudioRaiseVolume), same_screen YES,
    XLookupString gives 0 bytes:
    XmbLookupString gives 0 bytes:
    XFilterEvent returns: False
```
We see that the identifier is `XF86AudioRaiseVolume` and we can use that in our shortcuts.

Here is an example shortcut using a multimedia key:
```
{ 0, XF86XK_AudioRaiseVolume, spawn, SHCMD("pactl set-sink-volume @DEFAULT_SINK@ +5%") },
```
### Restart dwm
To restart dwm you can use a while loop in your `~/.xinitrc`:
```
while true; do
    dwm >/dev/null 2>&1
done
```
And this should replace the `exec dwm` line that may or may not be there already.
If you now try to quit dwm with Mod+Shift+Q it will launch again instantly, essentially restarting.

This comes in very handy when you are configuring stuff or applying patches as you need to restart for any changes to take effect.
## Programs that go well with dwm
### setxkbmap and xset
Setxkbmap and xset are both tools to configure your keyboard settings in Xorg.

Setxkbmap should already be on your system as it is installed with `xorg-server`.

Xset can be installed like this:
```
$ sudo pacman -S xorg-xset
```
Setxkbmap sets the keyboard layout and can be used like this:
```
$ setxkbmap LAYOUT VARIANT
```
`LAYOUT` is the country like `us` for example.

`VARIANT` is the variant of the keyboard layout, which can be `dvp` for example.

You can use `man xkeyboard-config` to see most of the layouts, variants and options, but not all of them.

You can find all of them at `/usr/share/X11/xkb/rules/evdev.lst`

Xset sets the typematic delay and rate and can be used like this:
```
$ xset r rate 200 30
```
With 200 being the typematic delay in ms and 30 being the typematic rate in Hz.

To make these settings persistent, add them to your `~/.xinitrc`:
```
setxkbmap us dvp &
xset r rate 200 30 &
while true; do
    dwm >/dev/null 2>&1
done
```
### xrandr
Xrandr is a tool from Xorg to manage multi monitor setups.

To install it:
```
$ sudo pacman -S xorg-xrandr
```
When you run `xrandr` without any arguments it will list all the available outputs:
```
$ xrandr
Screen 0: minimum 320 x 200, current 3840 x 1200, maximum 16384 x 16384
eDP-1 connected primary 1920x1080+0+0 (normal left inverted right x axis y axis) 309mm x 174mm
   1920x1080     60.05*+  60.01    59.97    59.96    59.93
   ...
   320x180       59.84    59.32
DP-1 connected 1920x1200+1920+0 (normal left inverted right x axis y axis) 519mm x 324mm
   1920x1200     59.95*+
   ...
   640x480       59.94
HDMI-1 disconnected (normal left inverted right x axis y axis)
DP-2 disconnected (normal left inverted right x axis y axis)
```
So 2 screens connected at the moment with `eDP-1` being my main screen (laptop) and `DP-1` being my monitor.

Say I would like to have my laptop screen below my monitor screen, I could run:
```
$ xrandr --output DP1 --auto --above eDP1 --auto
```
The --auto is for the resolution, I don't have the need to set it myself so I set it to automatic.

And the rest of the command is pretty simple, the alternatives to `--above` include: `--left-of`, `--right-of`, `--below` and `--same-as`.

And it is useful to add this command to your `~/.xinitrc`:
```
setxkbmap us dvp &
xset r rate 200 30 &
xrandr --output DP1 --auto --above eDP1 --auto &
while true; do
    dwm >/dev/null 2>&1
done
```
### feh
Feh can be used to view images and set your wallpaper, its pretty lightweight and minimal.

To install it:
```
$ sudo pacman -S feh
```
If you have an image you can set it as wallpaper like this:
```
$ feh --bg-fill path/to/wallpaper.img
```
`--bg-fill` will scale the image to fill the screen, there are many other options (man feh).

When you set a wallpaper with feh it will create or update the `~/.fehbg` file, which stores the command to restore your wallpaper after a reboot.

To have your wallpaper restored whenever you start dwm add it to your `~/.xinitrc`:
```
setxkbmap us dvp &
xset r rate 200 30 &
xrandr --output DP1 --auto --above eDP1 --auto &
~/.fehbg &
while true; do
    dwm >/dev/null 2>&1
done
```
This will restore your wallpaper whenever you run `startx`.
### picom
Picom is a compositor for Xorg, it makes effects like transparency and blur possible.

It's again a very lightweight program that fits dwm well in my opinion.

To install it:
```
$ sudo pacman -S picom
```
The config file is located at `~/.config/picom/picom.conf`

Very easy and straightforward to configure, the config file is very well documented and has a lot of comments explaing what everything does.

But here is what I would consider the most basic config:
```
# Shadows
shadow = false
shadow-radius = 12
shadow-opacity = 0.75
shadow-offset-x = -15
shadow-offset-y = -15
shadow-color = "#000000"
shadow-exclude = [
    "class_g = 'dwm'"
]
 
# Fading
fading = false
fade-in-step = 0.028
fade-out-step = 0.03
fade-delta = 10
 
# Transparency / Opacity
frame-opacity = 1.0
inactive-opacity = 1.0
active-opacity = 1.0
# opacity-rule = [
#     "90:class_g = 'st-256color'"
# ]
 
# Corners
corner-radius = 0
rounded-corners-exclude = [
    "class_g = 'dwm'"
]
 
# Blur
blur-method = "dual_kawase"
blur-strength = 5
 
# General Settings
backend = "glx"
vsync = true
detect-rounded-corners = true
detect-client-opacity = true
detect-transient = true
use-damage = true
glx-no-stencil = true
```
Literally everything apart from a few general settings is super straightforward, we are still using the window classes for rules just like in dwm.

All the `detect...` settings are set to true to detect and respect window properties instead of overwriting them, will lead to more accurate effects.

`use-damage` will redraw only the part of the screen that has actually changed instead of the whole screen every time if set to true, helps with performance.

`glx-no-stencil` will not use a stencil buffer if set to true, helps with performance.

I would suggest adding picom to your `~/.xinitrc` so that it starts automatically:
```
setxkbmap us dvp &
xset r rate 200 30 &
xrandr --output DP1 --auto --above eDP1 --auto &
~/.fehbg &
picom &
while true; do
    dwm >/dev/null 2>&1
done
```
### Redshift
Redshft can be used to adjust the color temperature of your screen which reduces eye strain in the evening or at night.

To install it:
```
$ sudo pacman -S redshift
```
The config file is located at `~/.config/redshift/redshift.conf`.

This is the most minimal config I have been able to make:
```
[redshift]
temp-day=6500
temp-night=3500
dawn-time=5:00
dusk-time=18:00
brightness-day=1.0
brightness-night=0.7
```
It is pretty self-explanatory, but here is me explainng all the options:

`temp-day` is the screen temperature during the day (between dawn and dusk).
`temp-night` is the screen temperature during the night (between dusk and dawn).
`dawn-time` is the time at which the day will start.
`dusk-time` is the time at which the night will start.
`brightness-day` is the brightness during the day (between dawn and dusk).
`brightness-night` is the brightness during the night (between dusk and dawn).

And to add to your `~/.xinitrc`:
```
setxkbmap us dvp &
xset r rate 200 30 &
xrandr --output DP1 --auto --above eDP1 --auto &
~/.fehbg &
picom &
redshift &
while true; do
    dwm >/dev/null 2>&1
done
```
### dwmblocks 
Dwmblocks is a third-party status bar for dwm.

To install it:
```
$ git clone https://github.com/torrinfail/dwmblocks
```
Or your own build as link.

Then:
```
$ cd dwmblocks
```
```
$ sudo make install
```

It works with different 'blocks' that can be updated individually.

These blocks are just pieces of text that can be either static or variable via a shell script.

Here is my config as an example:
```
static const Block blocks[] = {
	/*Icon*/ /*Command*/      /*Update Interval*/ /*Update Signal*/
	{"",     "sb-wifi",       5,                  0},
	{"",     "sb-brightness", 0,                  2},
	{"",     "sb-volume",     0,                  1},
	{"",     "sb-battery",    5,                  0},
	{"",     "sb-time",       5,                  0},
};
```
As you can see I have 5 different blocks, the 'command' column contains the shell scripts I have created and added to my path in order to execute them.

The update interval is how often the block updates in seconds, if it is set to 0 then it will only update via a signal.

These signals can be send like this:
```
$ kill -35 $(pidof dwmblocks)
```
-35 changes depending on the signal number, to get the number for the command, add 34 to the signal number and then get the negative of that number.

So number 1 = -35, because 1+34=35 and 35*-1=-35
