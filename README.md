# list usb
diskutil list (macos)

lsblk (linux)
# install image sur sdcard ou usb
sudo dd bs=1M if=chemin_vers_le_img_de_raspbian of=/dev/votre_carte status=progress conv=fsync

or use Raspberry Pi Imager

https://www.raspberrypi.com/software/
# configure boot usb
rendez vous dans la partition boot de la carte (la seule accessible depuis Windows, normalement) 

pour éditer le fichier config.txt et rajouter à la fin du fichier la ligne suivante :

program_usb_boot_mode=1

Verification : 

vcgencmd otp_dump | grep 17

17:3020000a  la réponse doit être cette valeur, sinon il est inutile de continuer

On peut maintenant effacer ou commenter la ligne program_usb_boot_mode=1

Vous pouvez éteindre le Raspberry Pi et enlever la carte micro SD.

# configure 3.5 inch screen
https://forums.raspberrypi.com/viewtopic.php?t=178443 

First of all enable SPI by using the terminal command:
raspi-config
Navigate to 'Advanced options' and then enable both SPI and SSH.

Reboot if needed through the command in the terminal:
sudo reboot

The touch screen should turn on and be white colored.

Give the following commands for Raspbian updating:
sudo rpi-update
sudo reboot

Then after restart:
sudo apt-get update
sudo apt-get upgrade
sudo reboot

sudo nano /boot/config.txt
then add this line to the bottom
dtoverlay=piscreen,speed=16000000,rotate=90
give Ctrl+X then save and exit.

Give the command:
sudo reboot

sudo apt-get install fbi
then
sudo reboot

After reboot give:
sudo nano /usr/share/X11/xorg.conf.d/99-fbturbo.conf
change the line in the file:
Option "fbdev" "/dev/fb0"
to
Option "fbdev" "/dev/fb1"
Give Ctrl+X, save, exit and reboot with sudo reboot.

To complete the installation create a script for touch axes:
sudo nano /etc/xdg/lxsession/LXDE/touchscreen.sh
then add these lines to the file:
DISPLAY=:0 xinput --set-prop 'ADS7846 Touchscreen' 'Evdev Axes Swap' 0
DISPLAY=:0 xinput --set-prop 'ADS7846 Touchscreen' 'Evdev Axis Inversion' 1 0
give Ctrl+X, save and exit.
Then make the file executable:
sudo chmod +x /etc/xdg/lxsession/LXDE/touchscreen.sh

Now let's run this script at startup:
sudo nano /etc/xdg/lxsession/LXDE/autostart
then before the line
@xscreensaver -no-splash
add
@lxterminal --command "/etc/xdg/lxsession/LXDE/touchscreen.sh"
give Ctrl+X, save, exit and reboot your RPi.
(Before 'command' there are two --, not one -)

# install soft keyboard

sudo apt-get update

sudo apt-get install libfakekey-dev libpng-dev libxft-dev autoconf libtool -y

git clone https://github.com/Elecrow-keen/matchbox-keyboard.git

cd matchbox-keyboard

chmod +x autogen.sh

sudo ./autogen.sh

sudo make

sudo make install

sudo apt-get install libmatchbox1 -y



sudo nano /usr/bin/toggle-matchbox-keyboard.sh

#!/bin/bash

#This script toggle the virtual keyboard

PID=`pidof matchbox-keyboard`

if [ ! -e $PID ]; then

killall matchbox-keyboard

else

matchbox-keyboard -s 50 extended&

fi

sudo chmod +x /usr/bin/toggle-matchbox-keyboard.sh

sudo nano /usr/local/share/applications/toggle-matchbox-keyboard.desktop

[Desktop Entry]

Name=Toggle Matchbox Keyboard

Comment=Toggle Matchbox Keyboard`

Exec=toggle-matchbox-keyboard.sh

Type=Application

Icon=matchbox-keyboard.png

Categories=Panel;Utility;MB

X-MB-INPUT-MECHANSIM=True

nano ~/.config/lxpanel/LXDE-pi/panels/panel

find 
Plugin {
  type=launchbar
  Config {
    Button {
      id=lxde-x-www-browser.desktop
    }
    Button {
      id=pcmanfm.desktop
    }
    Button {
      id=lxterminal.desktop
    }
    Button {
      id=wolfram-mathematica.desktop
    }
    Button {
      id=wolfram-language.desktop
    }
  }
}

add

Button {
id=/usr/local/share/applications/toggle-matchbox-keyboard.desktop
}


sudo reboot

