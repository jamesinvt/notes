0see buttons being pressed
```console
# libinput debug-events
```

see input devices
```console
# libinput list-devices
```

see wifi
```console
$ nmcli d wifi list
```

saved wifi
```console
$ nmcli c
```

info about pulse audio sink:
```console
$ pactl list sinks | grep -B1 -A9 State
```

info about cards``
```console
$ pactl list cards
```

bring back terminal background process
```console
$ fg
```

kernel logs in a nice format
```console
$ dmesg --color=auto --reltime --human --nopager --decode
```

colorful output of processes
`pidstat`

list window + apps running(get window ids here)
```console
xwininfo -root -children // get window id
xprop -id <window id> // query window
```

#commands #linux #terminal #cli