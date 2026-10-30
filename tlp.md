# TLP
## Installation
To install TLP, run the following command:

```$ sudo pacman -S tlp```

We also need to start/enable `tlp.service` like so:

```$ sudo systemctl start tlp```

```$ sudo systemctl enable tlp```
## Configuration
### Changing charging threshold
Edit `/etc/tlp.conf` and uncomment/add the following lines:
```
START_CHARGE_THRESH_BAT0=75
STOP_CHARGE_THRESH_BAT0=80
```
Charging will start when the battery is below 75% and will stop when it reaches or exceeds 80%.
### Changing profiles
There are 3 different default profiles which are all pretty well made.

The default that TLP uses is `performance` mode when on AC and `balanced` mode when on battery power.

There is also a `power-saver` mode which saves the most power.

If you'd like to have even more battery saving you can change the modes in `/etc/tlp.conf`, uncomment/add the following lines:
```
TLP_PROFILE_AC=BAL
TLP_PROFILE_BAT=SAV
```
Now we use the `balanced` profile when on AC and the `power-saver` mode when on battery power.
## Usage
To use tlp with the modes in the config file simply run:

```sudo tlp start```

And if you'd like to use a specific mode, run:

```$ sudo tlp mode```

Where *mode* is one of `performance`, `balanced`, and `power-saver`.
