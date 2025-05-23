# Interception Tools
With Interception Tools, you can modify your keyboard input system-wide and even create modifier layers and dual-function keys.

I use it personally for changing my CapsLock from the most useless key to the most useful one.

I do this by swapping Esc and CapsLock so that CapsLock serves as my Esc and the other way around, this is handy for vim motions as the Esc key can be hard to reach and cause strain.

I also have it so that when I hold CapsLock, it acts as Ctrl.

So pressing CapsLock+f is the same as Ctrl+f.

All of this is very handy as CapsLock has a very good place on the homerow but is rarely used.

To install Interception Tools, along with the variant that enhances CapsLock:

```$ sudo pacman -S interception-tools interception-caps2esc```

Then, create the udevmon.yaml file, used to configure Interception Tools:

```$ sudo vim /etc/interception/udevmon.yaml```

And have it contain the following:

```
- JOB: intercept -g $DEVNODE | caps2esc | uinput -d $DEVNODE
  DEVICE:
    EVENTS:
      EV_KEY: [KEY_CAPSLOCK, KEY_ESC]
```

Then, enable/start the udevmon service:

```$ sudo systemctl enable udevmon.service```

```$ sudo systemctl start udevmon.service```

And... Thats it! As simple as that, you have increased your ergonomics and speed on the keyboard by a lot.
