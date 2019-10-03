# PiCal
Wall mounted digital calendar using raspberry pi and Dakboard

# HOW TO BUILD A RASPBERRY PI-BASED CALENDAR

Get a $35 Raspberry Pi 3. The 3 is fast and includes Wifi so you don't need an extra adapter.

- 2.5A powersupply but some folks say you can run the Raspberry Pi off the monitor's USB power - IF that power can put out at least 1A. 500mA will likely cause instability. It depends on if you want to try to get the whole thing down to one power cable.

- Cheap Micro SD Card - 8 gigs is fine, but get whatever works for you. This doesn't need to be awesome.

- 1 foot HDMI cable. You're gonna mount the Raspberry Pi to the back of the monitor and hide it so you want the cable to be as small as possible.
You might need a 90 degree or 270 degree adapter to avoid HDMI cables from sticking out or a short cable with this built in.

-24" ish (smaller is fine) LCD (IPS is nice) monitor with smallish bezels and HDMI inputs that go out to the side (NOT directly out the back) as you want this flush on the wall.

## Install the raspberry pi
Download and put the most current https://www.raspberrypi.org/downloads/raspbian/ image to an SD Card. I use Noobs to bootstrap my install as it's super fast and easy. Go through the standard setup. Make sure you've set up:
Wifi login
Timezone
Boot to Desktop automatically

## Set up the operation system basics
After the first boot, the `raspi-config` utility should load up.
Connect to wifi
Make sure to enable `ssh access` and choose to boot into the graphical desktop mode.
Alternatively, raspi-config can be used in the terminal:
    Enter sudo raspi-config in a terminal window.
    Select Interfacing Options.
    Navigate to and select SSH.
    Choose Yes.
    Select Ok.
    Choose Finish.

# Sign up and configure DAKboard

If you haven’t already done so, create an account (free!) and configure DAKboard.

Note your Private URL, as we’ll need this later when configuring the Raspberry Pi. The Private URL can be found when editing a screen, under the “Settings & Defaults” tab

# Enabling SSH from GUI

If you prefer a GUI over the command line, perform the steps below:

    Open the "Raspberry Pi Configuration" window from the "Preferences" menu.
    Click on the "Interfaces" tab.
    Select "Enable" next to the SSH row
    Click on the "OK" button for the changes to take effect.

# Find the IP Address of Raspberry Pi and connect via ssh

In most cases your Raspberry Pi will be assigned a local IP address which looks like 192.168.x.x or 10.x.x.x. You can use various Linux commands to find the IP address.

I used ifconfig command here.
'ifconfig'

This command shows all the list of active network adapters and their configuration. The first entry(eth0) shows IP address as 192.168.2.105 which is valid.I have used Ethernet to connect my Raspberry Pi to the network, hence it is under eth0. If you use WiFi check under the entry named 'wlan0'.

use 'ssh pi@"IP ADDRESS" and enter password

# Update the OS
sudo apt-get update
sudo apt-get upgrade
sudo apt-get dist-upgrade

# Install some useful software
Install `unclutter` to hide the mouse cursor: `sudo apt install unclutter`.
Install `cec-utils` to control TV: `sudo apt install cec-utils`.

# Setting up NTP
This will sync the time time with ubuntu ntp server

sudo apt-get install ntpdate
sudo ntpdate -u ntp.ubuntu.com

# Now we’ll make a couple system configuration changes:
sudo nano /boot/config.txt
and add

#Display orientation. Landscape = 0, Portrait = 1
display_rotate=0

#Use 24 bit colors
framebuffer_depth=24


Hit Ctrl-O to save and Ctrl-X to exit

## Set up browser autostart & optimise

Edit the following file by entering `sudo nano /etc/xdg/lxsession/LXDE-pi/autostart` into terminal and add 

@xset s off
@xset -dpms
@xset s noblank
@chromium-browser --noerrdialogs --incognito --kiosk http://dakboard.com/app/?p="YOUR_PRIVATE_URL"
@unclutter -idle 0

Hit Ctrl-O to save and Ctrl-X to exit

   # Once everything is installed you should be able to control the tv using the command below:

Turn tv on: echo on 0 | cec-client -s -d 1
Turn tv off: echo standby 0 | cec-client -s -d 1
Tv status: echo pow 0 | cec-client -s -d 1
   
   # Troubleshooting Tips:

    Make sure your tv supports cec and that it is enabled. Tv manufactures call CEC by different names so you may have to do some research depending on your brand.
    Make sure you are using a new hdmi cable that is at least HDMI 1.2a

   # Different names for HDMI CEC

    Samsung – Anynet+
    Sony – BRAVIA Link or BRAVIA Sync
    Sharp – Aquos Link
    Hitachi – HDMI-CEC
    AOC – E-link
    Pioneer – Kuro Link
    Toshiba – Regza Link or CE-Link
    Onkyo – RIHD (Remote Interactive over HDMI)
    LG – SimpLink
    Panasonic – VIERA Link or HDAVI Control or EZ-Sync
    Philips – EasyLink
    Mitsubishi – NetCommand for HDMI
    Runco International – RuncoLink

# Turn the monitor on and off automatically (optional)
I am currently scripting a better solution.

To turn the monitor on/off on a daily schedule, grab this script "https://gist.github.com/AGWA/9874925" and put it in /home/pi/rpi-hdmi.sh. Next, make it executable:

chmod +x /home/pi/rpi-hdmi.sh

Now we’ll need to add a cron entry to call this script at the desired time, so open the cron editor:

crontab -e

And add the following lines at the bottom of the file:

# Turn HDMI Off (22:00/10:00pm)
0 22 * * * root /home/pi/rpi-hdmi.sh off

# Turn HDMI On (7:00/7:00am)
0 7 * * * root /home/pi/rpi-hdmi.sh on 

The  first number (0) is the minutes and the second number on each of those lines (22 and 7) is the hour in 24 hour time. So in this example, the monitor would turn off at 10:00pm and back on again at 7:00am. Adjust the time for your needs.

Keep in mind: this does not turn the Raspberry Pi off! It just turns off the monitor, saving energy and hopefully extending the life of your monitor. The Raspberry Pi is still on and running however.


# Outlook Calendar
My work uses Google Calendar (GSuite) and Outlook. We use Google Calendar at home. So If you want to sync Outlook->Google but ONLY including Personal/Podcasts/Travel categories. I categorize in Outlook at work, and then those appointments that are appropriate for the family calendar get moved over. Then the Family Calendar dashboard includes color coordinated items.

Outlook Google Calendar Sync open source project "https://github.com/phw198/OutlookGoogleCalendarSync?WT.mc_id=-blog-scottha"  can do this. It does require Outlook and is a client solution. 

# Reboot and it should all be fine :)
sudo reboot
    or
sudo shutdown -r 


Sources:
Setup: https://www.hanselman.com/blog/HowToBuildAWallMountedFamilyCalendarAndDashboardWithARaspberryPiAndCheapMonitor.aspx
Navigating Nano: http://write.flossmanuals.net/command-line/nano/
Alternative How To: http://www.instructables.com/id/Raspberry-Pi-Wall-Mounted-Google-Calendar/?ALLSTEPS
Alternative How To: https://gist.github.com/blackjid/dfde6bedef148253f987
Quick Bash Script: https://blog.mattwebb.io/2018/08/24/raspberry-pi-google-calendar-display/
CEC Controls https://timleland.com/raspberry-pi-turn-tv-onoff-cec/
Dakboard https://blog.dakboard.com/diy-wall-display/
CEC Commands: http://raspberrypi.stackexchange.com/questions/7054/cec-wake-up-command
Cron Script: https://gist.github.com/AGWA/9874925
