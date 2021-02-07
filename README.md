# Plan9 on Raspberry Pi (0 to 4)

## Richard Miller's Plan9 Rpi fork

### Make a bootable sdcard

```
curl -O https://9p.io/sources/contrib/miller/9pi.img.gz
gunzip 9pi.img.gz

# Replace mmcblkX with your correct sdcard device (find with lsblk)
dd if=9pi.img of=/dev/mmcblkX bs=1M status=progress
```


The sdcard should now boot as is on the rpi0 and rpi4 (rpi2 and rpi3 should also work).


### Get wifi working

Wifi requires an kernel cmdline argument to be added to the boot partition on the sdcard.
You could do this from within linux but here's how to do it from within the booted plan9 image.

```
# Boot the plan9 image you just made.
# In term window run:
dossrv; mount /srv/dos /n/9fat /dev/sdm0/dos

# boot partition on the sdcard is now mounted to /n/9fat
acme /n/9fat
```

* Right-click cmdline.txt in acme
* Append `ether1=type=4330` to the line.  ie:
`readparts=1 nobootprompt=local user=glenda ether1=type=4330`

* Save cmdline.txt (middle click `Put` in the window header)
* Reboot: `term% reboot`

The wifi device is now enabled in the kernel so we can now connect to a wifi network as per the wpa(8) man page example:

```
# Mount the device to /net/ether1 (loads the firmware too)
bind -a '#l1' /net
aux/wpa /2 -s 'your network id' -p /net/ether1
# prompted for password:
ip/ipconfig ether /net/ether1

ndb/cs # start connection service
ndb/dns -r # start dns resolver

ip/ping google.com  # test dns and network.  Press DEL to kill (similar to Ctrl-c on linux)
```

### Now what?

I'm not really sure yet.  There's no web browser in the standard plan9 distro.

You can "mount" remote servers easily:
```
# Browse sigrid's 9front code repos
9fs ftrv.se
ls /n/ftrv.se

acme /n/ftrv.se
```


## TODO: 9front.org image

9front is a fork of plan9 that is active and has a lot more software.  (not sure if rpi wifi is supported though)
