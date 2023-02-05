### 4 Install Docker

Up to this point, this has been standard PostmarketOS installation.

PostmarketOS supports Docker, so this makes installing Home Assistant (and other Docker containers) straight forward.

 In this short section, we install docker and docker-compose.
 
#### Caveats 

I have found two minor difficulties. Firstly, the Nexus5 system is 32 bit armv7. Most, but not every, docker image still supports this 32 bit architecture. Normally Docker will tell you if what you are trying to install isn't compatible. But most images are OK. Secondly, docker uses firewall rules that can cause problems. I'll come to this later.

#### 4.1) Install Docker

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

#### 4.2) Test  Docker

To check docker, run:
```
docker run hello-world
```
This should pull and run a very simple test that prints a message "Hello from Docker" and then exit.

#### 4.3) Clean up

Assumimg all is ok, run:
```
docker system prune -a
``` 
This will clean up and remove all docker stuff that is not being used.

