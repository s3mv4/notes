# Autologin
To automatically login to your Arch Linux device, you can create the following file:

```$ sudo mkdir /etc/systemd/system/getty@tty1.service.d```

```$ sudo vim /etc/systemd/system/getty@tty1.service.d/autologin.conf```

And have it contain the following:
```
[Service]
ExecStartPre=/usr/bin/nm-online -sq
ExecStart=
ExecStart=-/sbin/agetty -o '-p -f -- \\u' --noclear --autologin s3m %I $TERM
```

The bottom 2 lines are pulled from the [Arch Wiki](https://wiki.archlinux.org/title/Getty#Virtual_console), which works fine, but when changing wallpaper with my bing-wallpaper script, the internet connection is not ready which results in the script not updating the wallpaper.

So with the 'ExecStartPre' line at the top it makes sure the internet connection is up before continuing.
