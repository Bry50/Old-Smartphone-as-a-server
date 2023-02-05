### 3) PostmarketOS

With the PMbootrap program installed on your linux system, it is time to configure and install the Postmarket operating system onto the phone.

The official instructions for a Nexus5 are found [here](
https://wiki.postmarketos.org/wiki/Google_Nexus_5_(lg-hammerhead))
( If you have another device, search to find if your device specific instructions)
The official instructions contain lots and lots  of info which can confuse a beginner ( at least me). The following are the steps I used.

Starting from a standard, charged Nexus  5 device...

#### 3.1) Enable Fastboot mode
With Nexus 5 device powered off and USB cable unplugged, hold down Volume Down and Power buttons simultaneously for a few seconds, until something called Fastboot Mode screen  appears.   


#### 3.2) OEM unlock
Plug in the USB cable between PC and phone, then on the PC run:
```
$ sudo fastboot oem unlock
```
When prompted on the the phone device, use Volume Up/Down buttons to highlight 'Yes' choice, then press the Power button to select it.   

#### 3.3) Initialization
On the PC, run
```
$pmbootstrap init
```
This will give a series of options. Accept all defaults, except ( for the Nexus 5) choose:

Channel:                    Edge;                
Vendor:                      Lg ;                    
Device codename:  Hammerhead   
User Interface:.        fbkeyboard 
      
You'll be asked for a username . This will later be needed for login to the phone device ( both initial Screen and later SSH)

#### 3.4) Build images
On the PC, run
```
$ pmbootstrap install
```
This builds the boot and system images that are to be installed. 
You'll be asked to set a password for logging onto the phone device later on. ( Ignore any keymap warning)

#### 3.5) Flash  to phone 
Ensure device is still in fastboot mode ( ref 1 above), and plugged  in via USB. Then, on the PC, run:
```
fastboot format system    
pmbootstrap flasher flash_rootfs --partition userdata
pmbootstrap flasher flash_kernel
fastboot reboot
```
The format system gave me an error, but can be ignored.

Wait a while (a couple of minutes) as the phone reboots with PostmarketOS.
Note: I sometimes got boot error, in that case repeat from step 4 ( I suspect it is something to do with format system error, but I repeated until it worked !)
 
#### 3.6) Login and enable SSH
With the USB cable still connected, from your PC terminal ; with your user name run
```
$ssh user@172.16.42.1  
```
You will be asked for your password from step 4.

Hopefully, you now have control of the phone from your computer.

#### 3.7) Setup WIFI
To set up the WIFI, in the SSH session
```
$sudo nmtui
```
This gives a 'somewhat' intuitive screen where you  can add your WIFI credentials. 
(Use a google search for 'nmtui' for instructions if needed).

You'll probably want to set up a static IP address.

 Note: I ignorantly tried to install a 64 bit program (Nexus5 is 32bit armv7l) and crashed the system. Afterwards, it gave a random mac address after reboot. This screwed up the static IP address from my router. A static address can be set using nmtui  explained  [here](https://www.tecmint.com/nmtui-configure-network-connection/)

#### 3.8) Install Bluetooth

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

#### 3.9) Test Wifi 

Stop the SSH session ( type exit) and disconnect the USB cable.
From the PC, login using the wifi connection
```
$ssh user@192.168.X.XXX:21        
```

where 192.168.X.XXX is the IP address of your phone device. If you haven't setup a static address, use a network scanner to find the (lg-hammerhead) IP address.

#### 3.10) Check reboot

Now is a good time to check everything properly restarts after a system start, so run
```
sudo reboot
```
After the phone display comes back, login again and retest ( USB cable unplugged)
```
$ssh USER@192.168.X.XXX:21
```
Hopefully, no problems.....

You now have PostmarketOS on the phone

#### 3.11) Footnote:USB Power

This might be a good point to pause but before taking a break, there are a few points to note.

My power button does not shut the system down. You can power off in recovery mode ( fiddle with power and volume buttons), or be lazy like me, do nothing and let the battery discharge.

The Nexus5  should be capable detecting the capability of the connected USB power supply but this is not working for (my) PostmarketOS. 
I go more into the power supply in section 6. But if the battery is too low the phone will keep rebooting....

For me, even when plugged into a wall charger, 
```
$cat /sys/class/power_supply/bq24190-charger/input_current_limit
```
always returns 500000 microamps (0.5 amp), which is the limit that a computer usb port can provide . This isn't sufficient for the long term.

Assuming you plug into an at least 1 amp charger, this should correct the situation
```
$cd /sys/class/power_supply/bq24190-charger/
$sudo nano input_current_limit
```
Change value to 900000.

But if the phone reboots, you will need to change it again again. We will do more for the power supply in a following section.
