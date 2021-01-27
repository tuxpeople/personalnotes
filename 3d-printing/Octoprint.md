## Initial setup upon first boot
Update the system and change password and disable very unsecurely the sudo password

    ssh-copy-id -i ~/.ssh/id_rsa.pub pi@octopi
    ssh pi@octopi
    passwd pi
    sudo apt update
    sudo apt full-upgrade
    echo "pi ALL=(ALL) NOPASSWD: ALL" | sudo tee /etc/sudoers.d/010_pi-nopassw
    sudo reboot

Disable power on usb ports to prevent power leakage to printer over usb:

    ssh pi@octopi
    sudo wget https://raw.githubusercontent.com/tuxpeople/3dprinting/master/scripts/usb-poweroff.sh -O /usr/local/bin/usb-poweroff.sh
    sudo chmod +x /usr/local/bin/usb-poweroff.sh
    echo "@reboot root /usr/local/bin/usb-poweroff.sh" | sudo tee /etc/cron.d/usb-poweroff
    sudo reboot
    
## Create backup
(see https://community.octoprint.org/t/how-do-i-backup-my-octoprint-settings-on-octopi/1489)

    backupfolder="~/Downloads/octoprint"
    ssh pi@octopi
    sudo service octoprint stop
    exit
    rsync -auve ssh pi@octopi.local:/home/pi/.octoprint/ ${backupfolder}/
    ssh pi@octopi
    sudo service octoprint start
    exit
    
## Backup wiederherstellen
    backupfolder="~/Downloads/octoprint"
    ssh pi@octopi
    sudo service octoprint stop
  	rm -rf /home/pi/.octoprint
    exit
    rsync -auve ssh ${backupfolder}/ pi@octopi.local:/home/pi/.octoprint/
  	ssh pi@octoprint
    sudo service octoprint start
    
Create a list of the previously installed plugins:

    grep -A $(grep octoprint.plugin.core <localfolder>/logs/octoprint.log | awk '{ print $8 }') octoprint.plugin.core octoprint/logs/octoprint.log | grep -v "(bundled)"

Install all the plougins again via Settings > Plugin Manager > Get More...
