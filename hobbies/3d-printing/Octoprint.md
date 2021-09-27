## Initial setup upon first boot
Update the system, octoprint, pip and change password and disable very unsecurely the sudo password

    ssh-copy-id -i ~/.ssh/id_rsa.pub pi@octopi
    ssh pi@octopi
    passwd pi
    sudo apt update
    sudo apt full-upgrade
    ~/oprint/bin/pip install -U octoprint
    /home/pi/oprint/bin/python3 -m pip install --upgrade pip
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
    # Create a list of the previously installed plugins:
    grep -A $(grep octoprint.plugin.core .octoprint/logs/octoprint.log | grep "registered with the system" | awk '{ print $8 }' | head -1) octoprint.plugin.core .octoprint/logs/octoprint.log | grep "oprint/lib" | grep -v "(bundled)" | grep -v INFO | sort -u > .octoprint/installed-plugins.txt
    exit
    rsync -auve ssh pi@octopi.local:/home/pi/.octoprint/ ${backupfolder}/
    ssh pi@octopi
    sudo service octoprint start
    exit
    
## Restore backup
    backupfolder="~/Downloads/octoprint"
    ssh pi@octopi
    sudo service octoprint stop
  	rm -rf /home/pi/.octoprint
    exit
    cd ${backupfolder}
    rsync -auve ssh ./ pi@octopi.local:/home/pi/.octoprint/
  	ssh pi@octoprint
    sudo service octoprint start

Install all the plugins from ```installed-plugins.txt``` again via Settings > Plugin Manager > Get More...
