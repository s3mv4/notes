# Bluetooth
## Pairing a device
To use Bluetooth, we are going to need the following packages:

```$ sudo pacman -S bluez bluez-utils```

And enable/start the Bluetooth service:

```$ sudo systemctl enable bluetooth.service```

```$ sudo systemctl start bluetooth.service```

To pair a device, use the `bluetoothctl` command which comes with bluez-utils:

```$ bluetoothctl```

Then we enter the bluetoothctl cli and we can look for devices with:

```[bluetoothctl]> scan on```

Once you see the device you want to pair simply type 'connect' followed by the MAC-address of the device:

```[bluetoothctl]> pair 14:C1:4E:DF:A5:79```

After which the device is paired and connected.

To exit the bluetoothctl cli, press Ctrl+d or type 'exit'.
## Connecting to paired device
To connect to a paired device, we can once again enter the bluetoothctl cli, and type 'devices' whilst the scan option is off:

```[bluetoothctl]> devices```

This should list all connected devices, from this list you can gather the MAC-address of your paired device that you want to connect to and run:

```[bluetoothctl]> connect 14:C1:4E:DF:A5:79```

And your device should be connected.

To exit the `bluetoothctl` cli, press Ctrl+d or type 'exit'.
