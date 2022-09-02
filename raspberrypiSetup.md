## How to set up RaspberryPi and SSH

### ***UPDATED***
- Download the Raspberry Pi Imager
- Open Raspberry Pi imager and choose an OS. When choosing your OS, select `Raspberry Pi OS (other)`
- Select the OS you'd like to install (for headless, select Raspberry Pi OS Lite
- Click the Settings button in the bottom right of the Raspberry Pi Imager
- Check the `Enable SSH` box
- Check the `Set username and password` box and enter a username and password you'd like to use for SSH-ing into your raspberry pi
- You may configure Wi-Fi here as well but it is disabled by default if you want to just use ethernet
- Click `Save`
- Insert your micro SD card into the card reader and select that micro SD card as the storage in the Raspberry Pi Imager
- Click `Write`
- When it completes writing the image to the micro SD card, it will automatically eject the SD card
- Remove and reinsert the SD card into the reader
- You should see it labeled as `boot` in the Finder/File Explorer
- Add an extensionless file called `ssh` onto the SD card. Example: `touch /Volumes/boot/ssh`
- Eject the SD card and insert it into the Raspberry Pi SD card slot
- Plug the RP into the ethernet cable connected to the router, then plug in the RP power supply
- After a few minutes, the RP should be powered up and running.
- Go into your router settings and set a static IP address for the RP (different for every router)
- Open the terminal on your computer and enter `ping raspberrypi.local`. You should see that it is connected to the network. Cancel that connection once you've confirmed `ctrl-C`
- Get the SSH key for the RP by entering `ssh-keyscan -t rsa <your RP's IP address>`
- Copy that SSH key and add it to the end of your known_hosts file with the rest of your SSH keys
- Enter `ssh-keygen -R <your RP's IP address>` (tbh not sure what this does, something with SSH permissions)
- Now you can SSH into the RP with the credentials you set in the Raspberry Pi Imager by entering `ssh <RP username>@<RP IP>`
- It will then prompt you for the password that you set up on your RP, enter that now
- You're now SSH'd into your RP




----------------------------------

- Download desired img file (Raspian Lite is a good headless OS)
- Plug in MicroSD card to card reader
- Find the name of the MicroSD card
  - `diskutil list`
- Unmount the MicroSD card
  - `diskutil unmountDisk <disk name>`
- Write img to MicroSD card (You may have to install `pv` from homebrew if you don't have it, it just shows your write progress)
  - `pv <img file path> | sudo dd bs=1m of=<disk name>`
- Create your WPA Supplicant config file (find example alongside this README)
  - `vim /Volumes/boot/wpa_supplicant.conf`
- Add ssh file to activate ssh on the pi
  - `touch /Volumes/boot/ssh`
- Unmount the MicroSD card
  - `diskutil unmountDisk <disk name>`
- Plug MicroSD card into RaspberryPi and let it run for a few minutes. It will take some time to get the image mounted
- From computer, ping the pi to determine if it is connected to the local network
  - `ping raspberrypi.local`
- If successful, ssh into the pi
  - `ssh pi@<ip provided from ping>`
- The default ssh password is `raspberry`

## This section was stolen from LinusTechTips
https://linustechtips.com/topic/1094810-pi-hole-setup-tutorial/

 Stage 1 - OS Install/Setup:

    Before we can install Pi-Hole or anything else really, we have to setup our operating system of choice: Raspbian Buster Lite (stretch also works)
        Download and unzip the "Raspbian Buster Lite" image from the Raspbian website: https://www.raspberrypi.org/downloads/raspbian/
        Download and install balenaEtcher, our uSD card writer/burner of choice: https://www.balena.io/etcher/
        Plug in your uSD card
        Launch balenaEtcher, select the Raspbian Buster Lite image, your uSD card, and then click Flash. (https://i.imgur.com/GMSZj8Z.png)
    If you're doing a headless install like us (no monitor/keyboard required), you'll need to enable SSH before booting up the Raspberry Pi
        Replug your uSD card to allow Windows to recognize the new Raspbian partition layout
        You should have a lettered drive pop up marked as "boot" (https://i.imgur.com/4ar0ih3.png)
            If you don't, ensure your uSD is being detected in Disk Management (https://i.imgur.com/ZPmyyz6.png)
            Then assign the partition a drive letter: https://lmg.gg/8KVm6
        Create a file inside the "boot" folder called "ssh" with no extension (https://i.imgur.com/KDyB4nc.png)
            If you don't know how to make an extension-less file you can download it here: https://lmg.gg/8KVmb
    Plug your uSD card into the Raspberry Pi followed by networking, and then power.
    Since we're doing a headless install, we'll need to search for our raspberrypi's IP address so we can access it over SSH.
        If you know what you're doing, log in to your router's admin page and check the DHCP client/reservation list for "raspberrypi"
        If you don't know how to do the above, download Angry IP scanner and run it: https://lmg.gg/8KVmS
        Look for the hostname "raspberrypi", on that line the IP and MAC address of our Raspberry Pi will also be listed: 10.20.0.77 in our case (https://i.imgur.com/lK2ce0R.png)
    Now that we've found our Raspberry Pi's IP address + MAC Address, we need to assign it an INTERNAL/LOCAL static IP address.
        This process is going to vary wildly based on which router/DHCP server you use, so we'd recommend Googling your router's model name/number (can be found on the back) + "how to set static IP" (ex: "Netgear R7000 how to set static ip").
        If you're willing and somewhat tech savvy, you might also be able to figure it out on your own.
            Start by navigating to your router's admin page. The IP for this is typically located on a sticker on the back of your ISP's provided router (along with the admin page's default username and password), but you can also find it by running the command "ipconfig" in command prompt on a Windows PC. Your router's IP will be listed after "default gateway" (https://i.imgur.com/S2Ndc0w.png)
            Log in to the admin page either with the Iogin credentials listed on the back of the router, or by googling the model number of the router along with "default password". Some routers use a randomly generated default password, so googling will not work for those.
            Once logged in, look for a tab labeled "DHCP Reservation", "Static IP Assignment", or something along those lines. (https://i.imgur.com/FeMjd4V.png) You may have to go to the Advanced menu to access this. (https://i.imgur.com/6l4kIqH.png)
            Enter the MAC address we grabbed earlier with Angry IP scanner, and then enter/select your desired static IP address (make sure you're using something not taken by another device on your network). (https://i.imgur.com/znUTbKv.png)
            Hit Apply (or whatever the equivalent is for your router) 
    Re-plug the power connection for your Raspberry Pi, to allow it to restart and fetch it's newly assigned IP.
    To access the Raspberry Pi over SSH we will need to download and connect to it with an SSH client
        Download, install and then launch the SSH client of your choice.
            We will be using PuTTY because it's simple, but any SSH client will do: https://lmg.gg/8KVmQ (https://i.imgur.com/POLV3i4.png)
        Enter the newly assigned static IP address of your Raspberry Pi into PuTTY, and click "Open" (https://i.imgur.com/BegMcKC.png)
        After it prompts you with "login as:" enter "pi" (https://i.imgur.com/jfULCu5.png)
        Then for password, enter "raspberry". You should now be logged in over SSH. :D (https://i.imgur.com/Q058Sbw.png)
    Now that we're logged in over SSH, start by changing the default password, and updating the Raspberry Pi.
        To change the user password enter the command "passwd" and press enter.
            You'll then be prompted to enter the current password (this is "raspberry" so enter that)
            Then enter your desired new password
        To update the Raspberry Pi, run the command "sudo apt update" - this is going to update the package list to tell us if anything needs to be update. (https://i.imgur.com/ECpLG93.png)
            Then, to actually upgrade the packages now that the package manager knows which ones need updating, run "sudo apt upgrade -y". (https://i.imgur.com/EYfDhkC.png)
    Our Raspberry Pi is now updated, set to a secure password and ready to install Pi-Hole onto! :D
