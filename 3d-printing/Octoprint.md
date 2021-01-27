## Initial setup upon first boot
Update the system and change password

    ssh pi@octopi.local
    passwd pi
    sudo apt update
    sudo apt full-upgrade
    sudo reboot

Disable power on usb ports to prevent power leakage to printer over usb:

    ssh pi@octopi.local
    sudo apt -y install libusb-1.0-0-dev
    git clone https://github.com/mvp/uhubctl
    cd uhubctl
    make
    sudo make install
    
## Create backup
(see https://community.octoprint.org/t/how-do-i-backup-my-octoprint-settings-on-octopi/1489)

    ssh pi@octopi.local
    sudo service octoprint stop
    exit
    rsync -auve ssh pi@octopi.local:/home/pi/.octoprint/ <localfolder>/
    ssh pi@octopi.local
    sudo service octoprint start
    exit
    
## Backup wiederherstellen
    ssh pi@octopi.local
    sudo service octoprint stop
  	rm -rf /home/pi/.octoprint
    exit
    rsync -auve ssh <localfolder>/ pi@octopi.local:/home/pi/.octoprint/
  	ssh pi@octoprint.local
    sudo service octoprint start
    
Create a list of the previously installed plugins:

    grep -A $(grep octoprint.plugin.core <localfolder>/logs/octoprint.log | awk '{ print $8 }') octoprint.plugin.core octoprint/logs/octoprint.log | grep -v "(bundled)"

Install all the plougins again via Settings > Plugin Manager > Get More...
