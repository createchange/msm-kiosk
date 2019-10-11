Must run chrome in kiosk mode: "chrome --kiosk (url)"

Browser must be Chrome and must have this extension installed: https://chrome.google.com/webstore/detail/ignore-x-frame-headers/gleekbfjekiniecknbkamfmkohkpodhe/reviews

Installation Instructions

CentOS 7 Minimal

# Initial update and X / WM install
```
yum update
yum install epel-release
yum install git
yum groupinstall "X Window Server"
yum install openbox
```

# font install
```
yum install xorg-x11-font-utils fontconfig
yum install google-droid-sans-fonts
fc-cache -f -v
```

# text editor and chromium install
```
yum install vim
yum install chromium
```

# change user account to boot X into openbox
```
echo startx /usr/bin/openbox-session &> /dev/null >> /home/user/.xinitrc

```

# obtain MSM Kiosk files
```
cd /home/user
git clone https://gitlab.com/createchange/msm-kiosk.git
```

# Set start up defaults for openbox
```
vim /home/user/.config/openbox/autostart:
# Disable any form of screen saver / screen blanking / power management
xset s off
xset s noblank
xset -dpms

# Allow quitting the X server with CTRL-ATL-Backspace
setxkbmap -option terminate:ctrl_alt_bksp

# Start Chromium in Kiosk mode
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/' ~/.config/chromium/'Local State'
sed -i 's/"exited_cleanly":false/"exited_cleanly":true/; s/"exit_type":"[^"]\+"/"exit_type":"Normal"/' ~/.config/chromium/Default/Preferences
chromium-browser --disable-infobars --kiosk '/home/username/msm-kiosk/msmkiosk.html'
```

# Remove Right Click Menu from Openbox
``` 
In ```/etc/xdg/openbox/rc.xml``` remove block referring to Mouse binding for right click menu
```

# Set computer to autologin to user account

``` cp /usr/lib/systemd/system/getty@.service /usr/lib/systemd/system/getty@tty1.service```

In getty@tty1.service, edit the following line:

``` ExecStart=-/sbin/agetty  --noclear %I $TERM ```

and change to:

``` ExecStart=-/sbin/agetty --autologin username --noclear %I $TERM```

# Silence the machine

## GRUB Menu
Edit /etc/default/grub, changing the following values accordingly:
* GRUB_DEFAULT=0
* GRUB_TIMEOUT=0
* GRUB_HIDDEN_TIMEOUT=5
* GRUB_HIDDEN_TIMEOUT_QUIET=true

Then run:
```grub2-mkconfig -o /boot/grub/grub.cfg```