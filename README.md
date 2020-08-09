From https://community.ui.com/questions/Step-By-Step-Tutorial-Guide-Raspberry-Pi-with-UniFi-Controller-and-Pi-hole-from-scratch-headless/e8a24143-bfb8-4a61-973d-0b55320101dc:


[Step-By-Step Tutorial/Guide] Raspberry Pi with UniFi Controller and Pi-hole from scratch (headless)

Greetings,
Thought I'd make a step-by-step tutorial for anyone looking for a straight forward installation of both the UniFi Controller (version 5.11.X or lower) and Pi-hole (Advertisement Blocking DNS server) without needing to hook up the Raspberry Pi to a monitor with keyboard and mouse (headless). You can install one without the other as well.

If you still have questions or if I forgot to explain some part you're stuck at, please post here so I can add this to the tutorial.

Dutch version: https://gathering.tweakers.net/forum/list_messages/1873125

0. What you will physically need
- Raspberry Pi 3 model B(+) / Pi 4 with a MicroSD card of atleast 4GB

- Ethernet cable to connect it to an existing network

- capability to connect the MicroSD card to a computer via a card reader

1. Download the necessary tools and raspbian operating system image
1. Raspbian Buster Lite (= without desktop interface) through the ZIP option (or torrent).

https://www.raspberrypi.org/downloads/raspbian/

Direct: https://downloads.raspberrypi.org/raspbian_lite_latest

2. Rufus to install the image on to the MicroSD card.

https://rufus.ie/

Direct: https://github.com/pbatard/rufus/releases/download/v3.11/rufus-3.11.exe

3. Advanced IP Scanner to figure out the assigned IP address of your Raspberry Pi.

http://www.advanced-ip-scanner.com

Direct: http://www.advanced-ip-scanner.com/download/Advanced_IP_Scanner_2.5.3850.exe

4. If you are not using Windows 10, you can use PuTTY instead of the command prompt to connect to your Raspberry Pi.

https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

Direct: https://the.earth.li/~sgtatham/putty/latest/w64/putty-64bit-0.73-installer.msi

5. If you are having issues with the partitions on your MicroSD card, I recommend using MiniTool Partition Wizard. This program makes it very easy to start from scratch by removing every partition on your MicroSD and creating a new Fat32 partition.

https://www.partitionwizard.com/free-partition-manager.html

Direct: https://cdn2.minitool.com/download-center/release/products/pw/current-release/pw11-free.exe

2. Install Raspbian on the MicroSD
Open up Rufus to install Raspbian.

Make sure your MicroSD card is selected
Press SELECT and select the downloaded raspbian-stretch-lite.zip
Tip: you don’t have to extract the .zip

Press START
Accept the warning about removing all of the data on the MicroSD
wait until the process is done.
Windows may ask you to format the Linux partition, do not do this, just cancel the request.

3. Enable SSH
Enable SSH before starting the Raspberry Pi by adding a new text file to the main folder of the MicroSD card (boot partition).

Rename this text file to  ssh    and remove the .txt extension.


The MicroSD is ready now, insert it into your raspberry pi, connect an ethernet cable and turn on the Raspberry Pi.

Note: It is possible that Windows/Rufus has not assigned a driveletter to the "boot" partition. If you cannot see the drive in file explorer, do a right-mouse click on start, click on disk management and assign a letter to the "boot" partition by right clicking on the 256MB sized partition on your microSD card and assign a drive letter. You can also use the MiniTool Partition Wizard to do this.

4. Lookup the IP address
If you’re using Advanced IP Scanner, you can simply press SCAN and wait until the Raspberry Pi shows up.

5. Login via SSH
1. Open up the command prompt (cmd in start of windows)

Tip: You should be able to paste commands by right clicking into the command prompt.

.

2. Login with the SSH command

Note: change the IP address with the IP address of your Raspberry Pi

ssh 192.168.1.2 -l pi
3. As we have not defined an SSH key, it will ask if we trust the host.

The authenticity of host '192.168.1.2 (192.168.1.2)' can't be established. 
ECDSA key fingerprint is SHA256:XXX. Are you sure you want to continue connecting (yes/no)?   yes
4. When typing in the password, you will not see characters being displayed, simply type it and enter

The default password is:  raspberry
_____________________________________

Use the script to save time

I've added all the following commands into a simple script that I share through my GitHub project. By entering the line of code right after you login into your Raspberry Pi, you will not have to copy/paste all of these commands. You also get to choose your Stable version of the UniFi controller you wish to install.If you wish to check the script before executing, you can check it out at:https://github.com/SmokingCrop/UniFi/blob/master/install-unifi-pihole-English.sh

Copy/Paste this line of code to execute the script:

wget "https://github.com/SmokingCrop/UniFi/raw/master/install-unifi-pihole-English.sh" -O install-unifi-pihole.sh && chmod +x install-unifi-pihole.sh && ./install-unifi-pihole.sh
Or if you do not wish to install pi-hole, you can copy/paste this line of code to only install UniFi:(It's the same script, but with an argument at the end to not install pihole)

wget "https://github.com/SmokingCrop/UniFi/raw/master/install-unifi-pihole-English.sh" -O install-unifi-pihole.sh && chmod +x install-unifi-pihole.sh && ./install-unifi-pihole.sh no-pihole
If you do not wish to use the script, just continue following this tutorial.

 _____________________________________

6. Change the default password and Update all existing packages
It's very unsafe to keep the default password, so we'll change it.After entering the passwd command, it'll ask you to type the current password once and your new password twice.

pi@raspberrypi:~ $ passwd 
Changing password for pi. 
(current) UNIX password: 
Enter new UNIX password: 
Retype new UNIX password: 
passwd: password updated successfully
To make sure you have all the latest and greatest (firmware) updates and to clean up any unused/old ones, execute the following command:

 sudo apt-get update && sudo apt-get upgrade -y && sudo apt-get autoremove -y && sudo apt-get autoclean -y
7. Install Java 8
The default available Java version from Oracle is too old to make the 5.13.X UniFi controller work.Instead of doing multiple extra commands to upgrade this version, we're just going to install OpenJDK.

 sudo apt-get install openjdk-8-jre-headless -y
 8. Install haveged
In order to fix the slow start-up of the UniFi controller, we have to install haveged.The reason for the slow start-up is basically the lack of user interaction (no mouse movements) causing issues with the standard mechanisms for harvesting randomness. Luckily the fix is an easy one:

 sudo apt-get install haveged -y
9. Install the UniFi Controller
I'll show two ways of installing the UniFi Controller.

Method A: manually installing the Controller by using the download link on the website/update blog.

Easily install any Controller version you want, whenever you want. As soon as the blog comes out.
If you're not using the script, it'll take more commands to update your Controller in the future
Method B: Setting up the repository so you can install and update with the apt-get command.

Once it is setup, you can install/update the Controller like any other program with apt-get.
When a new Controller version is out, it takes like a week before you'll be able to install it.
It's recommended to still go the blog of the update to know if any changes will affect your setup!
9.1 Install the UniFi Controller with method A

 You can go to the (beta) updates blog and copy the Debian download link at the bottom of the posts (UniFi Controller for Debian/Ubuntu) if you want a different version than the one used in this tutorial.

https://community.ui.com/releases


1. Use the link with the wget command to download the Controller installation package.

wget https://dl.ui.com/unifi/5.13.32/unifi_sysvinit_all.deb
2. Install UniFi and its dependencies.

sudo dpkg -i unifi_sysvinit_all.deb; sudo apt-get install -f -y
Go to your UniFi Controller via the IP address and port:

E.g: https://192.168.1.2:8443

You’ll want to set a reserved IP in the UniFi Controller for the Raspberry Pi.

9.2 Install the UniFi Controller with method B

1. Install apt-transport-https in order to be able to use the 'deb' lines over HTTPS in the /etc/apt/sources.list

sudo apt-get install apt-transport-https -y
2. Add a new source to the list of sources from which packages can be obtained with 'apt'

echo 'deb https://www.ui.com/downloads/unifi/debian stable ubiquiti' | sudo tee /etc/apt/sources.list.d/100-ubnt-unifi.list
3. Add the GPG keys

sudo wget -O /etc/apt/trusted.gpg.d/unifi-repo.gpg https://dl.ui.com/unifi/unifi-repo.gpg
4. Update the packages after adding the new source and Install the UniFi package

sudo apt-get update && sudo apt-get install unifi -y
Go to your UniFi Controller via the IP address and port:

E.g: https://192.168.1.2:8443

You’ll want to set a reserved IP in the UniFi Controller for the Raspberry Pi.

10. Install Pi-hole
Execute the following command and configure Pi-hole

curl -sSL https://install.pi-hole.net | bash
> Choose an interface: (*) eth0  (= ethernet cable)  
> Choose your DNS provider: Custom 1.1.1.1, 8.8.8.8 (= fast cloudflare dns & basic Google dns)  or any other option
> Choose your adblocking providers: leave them all enabled  
> IPv4 and/or IPv6: both  
> PiHole wants to set a static IP. Let it do its thing. I guess they haven’t heard of DHCP reservation/central management yet.  
> Install the Web admin interface and the web server
> privacy mode for FTL: 0 Show everything
Change the password used to log into the web admin interface

pi@raspberrypi:~ $ pihole -a -p 
Enter New Password (Blank for no password): 
Confirm Password:   
[✓] New password set
Go to your Pi Hole web admin interface via the IP address and /admin:

E.g: http://192.168.1.2/admin

--I'm only able to write 10 000 characters per post. Check the accepted answer below--
