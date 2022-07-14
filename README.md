# list usb
diskutil list (macos)
lsblk (linux)
# install image sur sdcard ou usb
sudo dd bs=1M if=chemin_vers_le_img_de_raspbian of=/dev/votre_carte status=progress conv=fsync
# configure boot usb
rendez vous dans la partition boot de la carte (la seule accessible depuis Windows, normalement) pour éditer le fichier config.txt et rajouter à la fin du fichier la ligne suivante :
program_usb_boot_mode=1

# configure 3.5 inch screen
git clone https://github.com/Elecrow-keen/Elecrow-LCD35.git
cd Elecrow-LCD35
sudo ./Elecrow-LCD35
calibration:
cd Elecrow-LCD35
sudo dpkg -i -B xinput-calibrator_0.7.5-1_armhf.deb
Click the Menu button on the taskbar, choose Preference -> Calibrate Touchscreen.
Finish the touch calibration following the prompts. Maybe rebooting is required to make calibration active.
You can create a 99-calibration.conf file to save the touch parameters (not necessary if file exists).
/ect/X11/xorg.conf.d/99-calibration.conf

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

