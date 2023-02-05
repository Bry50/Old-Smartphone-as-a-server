# Home Assistant on old smartphone

Installing Home assistant (and other docker containers) onto an old smartphone isn't all that difficult.
Here is step by step guide to install PostmarketOS, then  Docker, then HomeAssistant onto an old Nexus5 phone

When I started with Home Assistant (HASS), the first question that arises is what hardware to use. I'll not go into other solutions, but for a long time, I was frustrated that I couldn't use old telephones lying in a drawer.

After many false starts, I read about PostmarketOS which totally replaces the Android system with and Alpine linux system, and importantly supports docker.

In this guide, I give the steps to install Home Assistant on an old Nexus 5 phone using PostmarketOS and Docker.

(If you read this on a phone, swipe right to see table of contents).

I hope it can be followed by a complete novice like me, but I can't fully explain everything. My intention is to give a minimum step by step to get things working and leave detailed explanations to your own googling follow up.

If you don't have a Nexus5, the PostmarketOS may have have a version for your device. But the large majority of devices are only partly converted by hobbyists. You might be lucky, but here only Nexus5 is addressed.....

The setup is surprisingly simple (once you know how...). Once you have the installation program setup; installing the system, docker and home assistant is simple; about 1 hour (but I have been through it several times). 

Tweaking the power supply etc is more intricate. For theNexus,the PostmarkerOS doesn't have power management properly implemented. Make sure you start with a charged battery. Once implemented, in Home assistant, we can fully control the battery,  even disconnect it.

For a simple server, we don't need display, audio, nor the phone functions. The essentials are:
    1) Power supply
    2) Wifi

Bluetooth is a nice to have

The full documentation for PostarketOS can be found [here](https://wiki.postmarketos.org/wiki/Main_Page)

Disclaimer: I presume you have an old phone that you can afford to bin if things go totally wrong. 

So far,during my inept amateurish fumbling down false paths, I have got into some situations where I thought the phone was bricked. But, so far,I have always been able to recover. 

 I'm pretty certain this guide works (today) for a Nexus 5 if followed step by step; but I can't provide further help.
 
Some steps just take a long time, don't panic too fast. If something has gone wrong (probably due to my inadequate explaination or low battery), just simultaneously hold down the power and volume down buttons untill the system reboots. If it doesn't, you'r probably out of power.

Being able to get back to the complete start point is a good thing if things go totally sideways. I use the [Wugfresh NexusToolKit](http://www.wugfresh.com/nrt/?noamp=mobile), old but it works for me.

Let's get started.
 