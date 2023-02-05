### 2) PMboostrap

PostmarketOS has an installation program called [PMbootstrap](https://wiki.postmarketos.org/wiki/Pmbootstrap). This takes care of rooting and all the stuff required to install the new system, installation is simple.

However, it only runs on  a linux system. If you already have one at your disposal, no problem, but I wasted a lot of time trying windows WSL (Windows Subsystem for Linux) without success. 
I eventually installed Ubuntu on an old laptop. 

The following are the steps for Ubuntu (other linux systems have slightly but annoyingly different commands):

### 2.1) Install PMbootstrap
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

### 2.2) Enable USB connection

In order to use a USB cable to communicate between the Android phone and the computer, something called 'USB debugging' needs to be enabled on the phone. 

Go to the phone Settings -> About -> Tap build number repeatedly until developer menu is enabled. Then tap on “ developer options ” and tick USB debugging.

A Google search eg ' Nexus 5 enable USB debugging' will give lots of instructions and videos if needed.

### 2.3) USB cable
I used to think all USB cables were more or less the same. But experience taught me that they are finiky and a source of problems.  A cable might work sometimes, but not other times. If things in the subsequent steps don't go right; try another cable.
