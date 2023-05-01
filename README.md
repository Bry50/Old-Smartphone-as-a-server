# Summary

An old smartphone can be used as a server by installing PostmarketOS.

This is step by step guide to install PostmarketOS, then  Docker, then Home Assistant onto an old Nexus5 phone and then control the battery charging.

# Contents

- [Introduction](#1Introduction)
- [PMbootrap](## PMbootstrap)
- [PostmarketOS](./3_PostmarketOS.md)
- [Docker](./4_Docker.md)
- [Home Assistant](./5_Home_Assistant.md)
- [Power Supply](./6_power_supply.md)
- [Remote Access](./7_swag_dashboard.md)

## 1 Introduction

When I started with Home Assistant, the first question that arises is what hardware to use. I started with an underpowered old raspberry PI and then moved to an old intel based server.
For a long time, I was frustrated that I couldn't use old telephones lying in a drawer.

After many false starts, I read about PostmarketOS which totally replaces the Android system with a small Alpine linux system, and importantly supports docker.

In this guide, I document the steps I used and provide links to  (often overwhelming) documentation.

If you don't have a Nexus5, I hope this guide can provide a guide for the steps involved. PostmarketOS may have have a version for your device. But, beware, the large majority of devices are only partly converted by hobbyists. You might be lucky, but here only Nexus5 is addressed.....

The setup is surprisingly simple (once you know how...). There is only one installation program. Installing the postmarket operating system, docker and home assistant is simple; about 1 hour if nothing goes wrong (but I have been through it several times). 

Tweaking the power supply is more intricate. PostmarketOS usually doesn't have power management properly implemented. Make sure you start with a charged battery. Once implemented, with Home assistant, we can fully control the battery,  even disconnect it!

For a simple server, we don't need display, audio, nor the phone functions. The essentials are a power supply and wifi; Bluetooth is a nice to have for homeautomation.

The full documentation for PostmarketOS can be found [here](https://wiki.postmarketos.org/wiki/Main_Page)

Disclaimer: I presume you have an old phone that you can afford to bin if things go totally wrong. 

But so far, during my inept amateurish fumbling down false paths, I have got into some situations where I thought the phone was bricked. But, so far, I have always been able to recover. 

I'm  pretty certain this guide works (today) for a Nexus 5 if followed step by step; but I can't provide further help for other devices.
 
Some steps can take a long time with no sign anything is happening; wait, don't panic too fast. If something has gone wrong (probably due to my inadequate explaination or low battery), just simultaneously hold down the power and volume down buttons untill the system reboots. If it doesn't, you're probably out of power.

Let's get started.

## 2 PMbootstrap

PostmarketOS has an installation program called [PMbootstrap](https://wiki.postmarketos.org/wiki/Pmbootstrap). This takes care of rooting and all the stuff required to install the new system; installation is simple.

However, it only runs on  a linux system. If you already have one at your disposal, no problem, but I wasted a lot of time trying windows WSL (Windows Subsystem for Linux) without success. 
I eventually installed Ubuntu on an old laptop. 

The following are the steps for Ubuntu (other linux systems have slightly but annoyingly different commands):

### 2.1 Install PMbootstrap
Open a terminal window ( CTRL+ALT+T) and run the following commands:
```
sudo apt update
sudo apt upgrade
sudo apt install python3-pip openssl git nano android-tools-adb android-tools-fastboot -y
sudo pip3 install --user pmbootstrap
sudo source ~/.profile
```
To test that pmbootstrap is installed correctly, run:
```
pmbootstrap --version
```
You should see the installed version.

If something has gone wrong, the official instructions can be found [here](
https://wiki.postmarketos.org/wiki/Installing_pmbootstrap)

### 2.2 Enable USB connection

In order to use a USB cable to communicate between the Android phone and the computer, something called 'USB debugging' needs to be enabled on the phone. 

Go to the phone Settings -> About -> Tap build number repeatedly until developer menu is enabled. Then tap on “ developer options ” and tick USB debugging.

A Google search eg ' Nexus 5 enable USB debugging' will give lots of instructions and videos if needed.

### 2.3 USB cable
I used to think all USB cables were more or less the same. But experience taught me that they are finiky and a source of problems.  A cable might work sometimes, but not other times. If things in the subsequent steps don't go right; try another cable and/or USB port.

### 3 PostmarketOS

With the PMbootrap program installed on your linux system, it is time to configure and install the Postmarket operating system onto the phone.

The official instructions for a Nexus5 are found [here](
https://wiki.postmarketos.org/wiki/Google_Nexus_5_(lg-hammerhead))
( If you have another device, search to find if your device specific instructions)
The official instructions contain lots and lots  of info which can confuse a beginner ( at least me). The following are the steps I used.

Starting from a standard, charged Nexus  5 device...

#### 3.1 Enable Fastboot mode
With Nexus 5 device powered off and USB cable unplugged, hold down Volume Down and Power buttons simultaneously for a few seconds, until something called Fastboot Mode screen  appears.   


#### 3.2 OEM unlock
Plug in the USB cable between PC and phone, then on the PC run:
'''
$ sudo fastboot oem unlock
'''
When prompted on the the phone device, use Volume Up/Down buttons to highlight 'Yes' choice, then press the Power button to select it.   

#### 3.3 Initialization
On your PC, run
```
$pmbootstrap init
```
This will give a series of options. Accept all defaults, except ( for the Nexus 5 which is LG device named Hammerhead) choose:

Channel:                    Edge;                
Vendor:                      Lg ;                    
Device codename:  Hammerhead   
User Interface:.        fbkeyboard 
      
You'll be asked for a username . This will later be needed for login to the phone device ( both initial Screen and later SSH)

#### 3.4 Build images
On your PC, run
```
$ pmbootstrap install
```
This builds the boot and system images that are to be installed. 
You'll be asked to set a password for logging onto the phone device later on. ( Ignore any keymap warning)

#### 3.5 Flash  to phone 
Ensure phone device is still in fastboot mode ( ref 3.1 above), and plugged  in via the USB. Then, on the PC, run:
```
fastboot format system    
pmbootstrap flasher flash_rootfs --partition userdata
pmbootstrap flasher flash_kernel
fastboot reboot
```
The format system gave me an error, but can be ignored.

Wait a while (a couple of minutes) as the phone reboots with PostmarketOS.
Note: I sometimes got boot error, in that case repeat from step 4 ( I suspect it is something to do with format system error, but I repeated until it worked !)
 
#### 3.6 Login and enable SSH
With the USB cable still connected, from your PC terminal ; with your user name run
```
$ssh user@172.16.42.1  
```
You will be asked for your password from step 4.

Hopefully, you now have control of the phone from your computer.

#### 3.7 Setup WIFI
To set up the WIFI, in the SSH session run
```
sudo nmtui
```
This gives a 'somewhat' intuitive screen where you  can add your WIFI credentials. 
(Use a google search for 'nmtui' for instructions if needed).

You'll probably want to set up a static IP address for the phone on your router or preferably using nmtui

Note: I ignorantly tried to install a 64 bit program (Nexus5 is 32bit armv7l) and crashed the system. Afterwards, it gave a random mac address after reboot. This screwed up the static IP address from my router. A static address can be set using nmtui  explained  [here](https://www.tecmint.com/nmtui-configure-network-connection/)

#### 3.8 Install Bluetooth

I use Homeassistant to read  bluetooth BLE temperature sensors using the 'HACS Passive BLE Monitor' integration using the phone's bluetooth capability.

Setting up the Bluetooth can be done later or skipped.

To set up bluetooth ; 
```
sudo apk add bluez
sudo service bluetooth start
sudo rc-update add bluetooth boot
bluetoothctl power on
bluetoothctl  scan le
```
This will add bluetooth software, set it to start  after a reboot, and start a scan for bluetooth low energy ( le) devices. Ctrl+C to stop.

But the default config needs to be tweaked to start everything, so 
```
sudo nano /etc/bluetooth/main.conf 
```
and almost at the end of all the commented-out configuration options, configure
```
autoenable=true
```

#### 3.9 Test Wifi 

Stop the SSH session ( type exit) and disconnect the USB cable.
From the PC, login using the wifi connection
```
$ssh user@192.168.X.XXX:21        
```

where 192.168.X.XXX is the IP address of your phone device. If you haven't setup a static address, use a network scanner to find the (lg-hammerhead) IP address.

#### 3.10 Check reboot

Now is a good time to check everything properly restarts after a system start, so run
```
sudo reboot
```
After the phone display comes back, login again and retest using wifi (USB cable unplugged)
```
$ssh user@192.168.X.XXX:21
```
Hopefully, no problems.....

You now have PostmarketOS on the phone

#### 3.11 Footnote: USB Power

This might be a good point to pause but before taking a break, there are a few points to note.

My power button does not shut the system down. You can power off in recovery mode ( fiddle with power and volume buttons), or be lazy like me, do nothing and let the battery discharge.

Attention: The Nexus5  should be capable detecting and setting the input_current_limit of the connected USB power supply, but this is not working for (my) PostmarketOS. (I go more into the power supply in section 6).

For me, even when plugged into a wall charger, 
```
cat /sys/class/power_supply/bq24190-charger/input_current_limit
```
always returns 500000 microamps (0.5 amp), which is the limit that a computer usb port can provide . This isn't sufficient and the battery will run down and the device will start to continually reboot.

Assuming you plug into an at least 1 amp charger, this should correct the situation
```
sudo nano /sys/class/power_supply/bq24190-charger/input_current_limit
```
And set the value to 900000.

If you disconnect the power or the phone reboots, this will need to be done again.

### 4 Docker

Up to this point, this has been standard PostmarketOS installation.

PostmarketOS supports Docker, so this makes installing Home Assistant (and other Docker containers) straight forward.

 In this short section, we install docker and docker-compose.

#### 4.1 Install Docker

From now on, I assume you are connected to the Nexus5 by (wifi) SSH.

Following the instructions for installing  Docker on [Alpine Linux](https://wiki.alpinelinux.org/wiki/Docker)
```
sudo apk upgrade
sudo apk update
sudo apk add docker
sudo addgroup bryan docker         
sudo rc-update add docker boot
sudo service docker start
sudo apk add docker-compose
sudo reboot
```
Obviously change 'bryan' for your user name...

#### 4.2 Test  Docker

To check docker, run:
```
docker run hello-world
```
This should pull and run a very simple test that prints a message "Hello from Docker" and then exit.

#### 4.3 Clean up

Assuming all is ok, run:
```
docker system prune -a
``` 
This will clean up and remove all docker stuff that is not being used.

### 5 Home assistant

We'll now use docker-compose to install Home Assistant along with an MQTT broker.

The Home Assistant site gives instructions how to set up using docker-compose [here](https://www.home-assistant.io/installation/linux/#docker-compose).

In theory, you can follow those instructions. However, I think it is better to create storage volumes first because, if you let  Docker make them, you will not have  permissions to modify them outside of Home assistant.

#### 5.1 Create storage volumes
So,  to create the storage volumes on the PostmarkerOS phone I suggest:
```
mkdir ~/volumes
mkdir ~/volumes/hass
mkdir ~/volumes/mosquitto
mkdir ~/volumes/mosquitto/config
```
The mosquitto MQTT broker needs a config file, create it with :
```
nano ~/volumes/mosquitto/config/mosquitto.conf
```
Set the configuration as:

```
allow_anonymous true
listener 1883 0.0.0.0
# logging
log_type none  
```
( Ctrl- O & Ctrl-X to save and exit)

#### 5.2 Docker firewall

Docker is designed to be run in a business environment and sets up firewall rules between containers using outdated technology ('iptables').

PostmarketOS uses  'nftables' and the MQTT container could not communicate with Home Assistant (because it uses docker network 'host' mode).

So.....to allow the MQTT container to talk to the Home Assistant container, I decided to stop the postmarketos internal firewall (nothing to do with the router firewall).

Yeah... I know, not ideal...but.. I haven't found a simple solution.

issue the following commands (to stop the  filewall starting onboot)
```
$ sudo rc-update del nftables
$ sudo reboot
```
(Note: 'sudo rc-update add nftables' would put them back again)


#### 5.3 docker-compose

The docker-compose.yml file groups all the docker commands needed to load the  systems we want; currently HomeAssistant and MQTT. 

Where you put this file isn't critical; to keep things simple I suggest you create it in the home directory (~).

Note: To create a text file, the Linux command line editors (eg nano) are ...what can I say..... geeky . You can use Nano that was  installed during the earlier setup or install another editor. I personally use 'CX file explorer' on my normal android phone and connect to the Nexus via SFTP.

Whatever,  create the file 'docker-compose.yml' 
and insert the following for homeassistant and mqtt services
```
version: '3'
services:
  homeassistant:
    container_name: homeassistant
    image: "ghcr.io/home-assistant/home-assistant:2022.7.1"
    volumes:
      - /home/bryan/volumes/hass:/config
      - /etc/localtime:/etc/localtime:ro
      - /sys:/sys
    network_mode: host
    restart: unless-stopped
    depends_on:
      - mqtt
  
  mqtt:
    container_name: mqtt    
    image: "eclipse-mosquitto:1.6.13"
    volumes:
      - /home/bryan/volumes/mosquitto/config:/mosquitto/config
      - /home/bryan/volumes/mosquitto/log:/mosquitto/log
      - /home/bryan/volumes/mosquitto/data:/mosquitto/data
      - /etc/localtime:/etc/localtime:ro
    network_mode: host
    restart: unless-stopped
```

Obviously, change 'bryan' to your user name.

The versions (eg :1.6.13) are optional, but I like to 'freeze' the versions to avoid surprises
  
This contains the instructions to pull ( download) the standard docker 'image' to build your working 'container'. The volumes tell it where to store data and the network mode 'host' tells it not to isolate  containers in a separate network as Docker would normally do.

#### 5.4 Docker-compose up

When run for the first time, the following instruction will cause docker  to pull (download) and extract the files. This is time consuming on the Nexus and needs the battery..

```
sudo apt update
sudo docker-compose pull
```

When everything finishes downloading,  run
```
sudo docker-compose  up -d 
```
This will start Home Assistant; the -d flag is detached mode which gives you back control of the console.

Wait a few minutes as things take a little time to set up the first time.

To access Home Assistant, from any machine on your local network, use an internet browser to connect to 192.168.X.XXX/8123.

You should have access to the normal home assistant log-in screen.

## 6 Power supply

The Nexus5 power  is (mostly) controlled by a bq24192 chip. This section applies to devices with that chip but might provide usefull ideas for other devices.

The PostmarketOS hasn't fully implemented power management. However,  as a server, the Nexus5 will have a permanent USB adapter power supply, conserving battery charge is not the problem; but we need to not overcharge the battery.

In this section, we will create a script that changes some battery related file permissions so that we can actually control (and even disconnect) the battery using Home Assistant (Hass).

But firstly, (as mentioned before) the system does not correctly recognise the power capacity of the connected USB power supply. It limits the input to 500 mA that a standard PC USB connection can reliably supply. This isn't enough (mine needs about 800 mA) and will eventually run down the battery. 

Assuming that you have pluged the Nexus into a capable power charger, we need to increase the 'input current limitation'.

I found increasing the limit to ~1 Amp adequate and can be done by 
```
sudo nano /sys/class/power_supply/bq24190-charger/input_current_limit
```
And set the value to 900000.

Next, we can set up sensors in Hass to
determine the current battery charge percentage, the current battery voltage and whether the charge type is 'fast' or 'trickle'

```
sensor:
  - platform: command_line
    name: Nexus Battery Level
    command: "cat /sys/class/power_supply/battery/capacity"
    unit_of_measurement: "%"
    value_template: "{{ value | multiply(1) | round(0) }}"
    
  - platform: command_line
    name: Nexus Voltage Now
    command: "cat /sys/class/power_supply/battery/voltage_now"
    unit_of_measurement: "V"
    value_template: "{{ value | multiply(0.000001) | round(2) }}"
        
  - platform: command_line
    name: Nexus Charge Type
    command: "cat /sys/class/power_supply/bq24190-charger/charge_type"
```


Sensors are fine to see what is the status, but if we want to control the battery status, we will need to write data to Nexus  files. 
The following script will, on startup,  change file permissions and set some initial values to limit the battery charging. 

```
sudo nano /etc/local.d/bq24190-charger.start
```
Then enter the following contents
```
#!/bin/ash

# Increase the input current limit
# IMPORTANT This assumes >1amp USB supply
chmod 777 /sys/class/power_supply/bq24190-charger/input_current_limit
echo 1000000 /sys/class/power_supply/bq24190-charger/input_current_limit

# Reduce battery charge voltage to 3.6V
chmod 777  /sys/class/power_supply/bq24190-charger/constant_charge_voltage
echo 3600000 > /sys/class/power_supply/bq24190-charger/constant_charge_voltage

# Start with Trickle charge 
chmod 777  /sys/class/power_supply/bq24190-charger/charge_type
echo Trickle > /sys/class/power_supply/bq24190-charger/charge_type

# Start with charging enabled
chmod 777  /sys/class/power_supply/bq24149-charger/f_chg_config
echo 1  /sys/class/power_supply/bq24149-charger/f_chg_config

# Disable Charging Safety Timer 
chmod 777  /sys/class/power_supply/bq24149-charger/f_en_timer
echo 0  /sys/class/power_supply/bq24149-charger/f_en_timer

# Turn off display brightness
chmod 777 /sys/class/backlight/lcd-backlight/brightness
echo 0 /sys/class/backlight/lcd-backlight/brightness
```

Exit nano saving the file  and make the script executable:
```
sudo chmod u+x /etc/local.d/bq24190-charger.start
```

Now we have control over the battery. For example, the following home assistant configuration will allow you to select the 
battery charge voltage and trigger an automation that sends the necessary command when it changes:
```
input_select:
  charge_voltage:
    name: Charge Voltage
    options:
      - 3504000
      - 3632000
      - 3888000
      - 4144000
      - 4272000
    initial: 3632000

shell_command:
  set_charge_voltage: /bin/bash -c "echo '{{ states('input_select.charge_voltage')|int }}' >> /sys/class/power_supply/bq24190-charger/constant_charge_voltage"
 
automation:
  - alias: "Charge Voltage"
    trigger:
      platform: state
      entity_id: input_select.charge_voltage
    action:
      service: shell_command.set_charge_voltage
```

You should set up a similar automation to change the 'input_current_limit'. Also, as the value can drop down if the usb power is interrupted, set a time pattern trigger.


## 7) Wrap up

That's it. I hope you found this guide informative.

With docker, it's easy to set up other packages. The Nexus5 has 32GB of memory and it surprised me what can be done. My Home Assistant is minimal (most temperature sensors) and I have also running
   Wireguard VPN server
   Wordpress & maraidb web server
   Crowdsec security
   Swag/Letsencrypt remote access
   Jupyter Notebook server
   
Hope you have fun.......



