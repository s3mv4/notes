# Graphical user interface setup guide
## Display server
Xorg or X is the most popular display server for Linux.

A display server is needed to have a GUI and basically sits between your hardware and the graphical environment (like a window manager or a desktop environment).

To install it, we need a couple of packages which I will explain below:
```
$ sudo pacman -S xorg-server xorg-xinit
```
`xorg-server` being the actual display server.

`xorg-xinit` is the package that allows us to start the display server from the command line (avoiding the need for an external display manager).
## Graphical environment
The graphical environment can either be a window manager or a desktop environment.

Desktop environments are basically the full package and come with a window manager, file manager, panel, taskbar and a whole lot more. Popular desktop environments are KDE and GNOME.

Desktop environments are usually a lot more mouse driven, while standalone window managers more often than not rely on various keyboard shortcuts for proper usage.

Window managers are way more lightweight than desktop environments and therefore won't hog as much RAM. I also like the more keyboard centered approach they have and don't mind the extra hassle of setting it up as once it's done, it's done. I can just put my window manager on github and just clone it from there whenever I'm reinstalling my system.
### dwm (thorough guide see dwm.md)
My preferred window manager is dwm, it is written in C and does not have any config files but rather the user needs to modify the source code to tweak it to their needs. You don't have to update it if you don't want to and that way it can be really stable too.
#### Installation and first run
To install dwm, we need to clone it from the suckless (creators) repository:
```
$ git clone https://git.suckless.org/dwm
```
If you already have a dwm build then you can obviously change the url to the github repository of your dwm build
Then we can change directory to dwm:
```
$ cd dwm
```
And to build it we first need a few libraries:
```
$ sudo pacman -S libxft libxinerama
```
Then we can do:
```
$ sudo make install
```
And dwm should be built and ready to go.

To start it, we first need to create ~/.xinitrc so the `startx` command knows what to do whenever it's executed:
```
$ vim ~/.xinitrc
```
And add the following to the file for now:
```
exec dwm
```
We also need a font for dwm to use (you can change it later):
```
$ sudo pacman -S terminus-font
```
And now we should be able to start dwm for the first time, run:
```
$ startx
```
It should start dwm but we can't do anything yet because we don't have any other programs installed yet.

Quit dwm with Alt+Shift+Q.
### Programs
#### dmenu
Dmenu is another suckless program that is used to launch applications, you can create your own build but it's, for the most part (atleast for me) not necessary. 

To install and it:
```
$ sudo pacman -S dmenu
```
Or, alternatively, for the developement version you can get it directly from suckless:
```
$ git clone https://git.suckless.org/dmenu
```
```
$ cd dmenu
```
```
$ sudo make install
```
It can be launched with Mod+p if you are in dwm (for Mod see dwm.md)
#### st
St is a terminal emulator that provides terminal functionality within the graphical environment, essentially a window that we can use to run commands like the tty.

St is also a suckless tool that I would create your own build of because this is the #1 program you will be using in your workflow in a window manager.

But to get the suckless build:
```
$ git clone https://git.suckless.org/st
```
```
$ cd st
```
```
$ sudo make install
```
#### Firefox
My preferred browser is Firefox because it has accounts which are useful for saving bookmarks, passwords and history across literally any device and it's open source (only maybe a bit bloated but it's fine).

To install:
```
$ sudo pacman -S firefox
```
When prompted for providers for jack, select option 2: `pipewire-jack`. I will touch on this in a bit in the audio section.

When prompted for ttf-font providers, just get `gnu-free-fonts` (If you install firefox before the first run of dwm, you won't need `terminus-font`).
### Audio
You can use pipewire or pulse-audio for your audio.

Pipewire is more modern and better in every area than pulse-audio so we will be using that.

For pipewire you need 3 packages: `pipewire`, `pipewire-audio` and `pipewire-pulse`.

If you installed Firefox, `pipewire` and `pipewire-audio` already came with it, so you don't need to install the again and only need `pipewire-pulse`.

To install:
```
$ sudo pacman -S pipewire pipewire-audio pipewire-pulse
```
Or alternatively, if you have installed Firefox:
```
$ sudo pacman -S pipewire-pulse
```
Now reboot:
```
$ reboot
```
And audio should be working fine, test this in the browser with a YouTube video or something.