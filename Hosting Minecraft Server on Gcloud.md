Hosting Minecraft server in Google cloud platform

[https://cloud.google.com/solutions/gaming/minecraft-server](https://cloud.google.com/solutions/gaming/minecraft-server)

[https://medium.com/@manbobo2012/host-a-minecraft-server-on-google-cloud-with-automatic-deployment-and-backup-f00d49a1a306](https://medium.com/@manbobo2012/host-a-minecraft-server-on-google-cloud-with-automatic-deployment-and-backup-f00d49a1a306)

Host Attack of B-team Modpack.

Setup new project in Google Cloud

This will help determine the actual cost every month. Separate from other projects

Create a VM instance

4 CPU

8GB RAM

20GB disk

Static External IP

Firewall rules for TCP port : 25565

SETUP Guide

[Gcloud documentation on MC Server](https://cloud.google.com/solutions/gaming/minecraft-server)

SSH into VM instance
```
sudo -s
```

Runs all commands followed as root
```
apt-get update
```

Download Java 8 jre: [https://www.oracle.com/java/technologies/javase-downloads.html](https://www.oracle.com/java/technologies/javase-downloads.html)

Upload file.

Move file to /opt
```
cd /opt
```

```
sudo tar -zxvf server-jre-7u80-linux-x64.tar.gz
```

To unpack the folder
```
update-alternatives --install /usr/bin/java java /opt/jdk1.7.0_80/bin/java 1
```
```
update-alternatives --install /usr/bin/javac javac /opt/jdk1.7.0_80/bin/javac 1
```

Check Java version: java -version
```
cd /opt
```

```
mkdir minecraft
```

```
cd minecraft
```

Upload Forge jar file. And move it to /opt/minecraft

Forge version should be the same as the version of MC you are running

[https://files.minecraftforge.net/](https://files.minecraftforge.net/)
```
java -jar forge-1.6.4-9.11.1.965-installer.jar --installServer
```

RUN FILE
```
minecraftforge-universal-1.6.4-9.11.1.965-v164-pregradle.jar
```

```
cd /home
```

```
mkdir minecraft
```

```
cd minecraft
```

```
wget [https://servers.technicpack.net/Technic/servers/bteam/BTeam_Server_v1.0.12c.zip](https://servers.technicpack.net/Technic/servers/bteam/BTeam_Server_v1.0.12c.zip)
```

```
apt install unzip
```

```
unzip BTeam_Server_v1.0.12c.zip
```
```
ls
```

```
nano eula.txt
```
change:
Eula=true
```
java -Xmx14663M -Xms4096M -jar minecraft_server.1.6.4.jar nogui
```

Save this command it will be the command that will be used to start the server every time
```
stop
```

Will stop the server
```
cd /home/minecraft
```


# Gcloud

Use SSH gear to upload world save file. Saves in users home directory

```
mv KillAllZombie.zip /home/<username> /home/minecraft
```

```
mv KillAllZombie.zip /home/rjslick88_gmail_com /home/minecraft
```

```
Unzip KillAllZombie.zip
```

```
java -Xmx14336M -Xms4096M -jar minecraftforge-universal-1.6.4-9.11.1.965-v164-pregradle.jar nogui
```

To start Java server again
```
apt-get install -y screen
```

```
screen -S mcs java -Xmx7168M -Xms2048M -jar minecraftforge-universal-1.6.4-9.11.1.965-v164-pregradle.jar nogui
```

Detach the screen terminal by pressing Ctrl + a, then typing d. The terminal will continue to run in the background. To reattach the terminal, run screen -r <terminal_name>

```
screen -r mcs
```

```
nano server.properties
```

Change level-name

Save
```
nano ops.txt
```

Add usernames for admin rights on server

RJ_HP860

Save

HELPFUL LINKs

 [full setup guide](https://www.linuxnorth.org/minecraft/modded_linux.html#Step%208%20-%20Hardening%20Your%20Minecraft%20Server%20if%20Visible%20on%20the%20Internet) for modded server

## ERROR?

Can not connect to SSH. Getting OS Login Error

Open gcloud console and run:
```
gcloud compute project-info add-metadata \  
    --metadata enable-oslogin=TRUE
```

Startup-script
```
#!/bin/bash

mount /dev/disk/by-id/google-minecraft-disk /home/minecraft

(crontab -l | grep -v -F "/home/minecraft/backup.sh" ; echo "0 */8 * * * /home/minecraft/backup.sh")| crontab -

cd /home/minecraft

screen -d -m -S mcs cd /opt/minecraft && java -Xmx7168M -Xms2048M -jar /opt/minecraft/minecraftforge-universal-1.6.4-9.11.1.965-v164-pregradle.jar nogui
```

Oracle Java links:

[https://www.oracle.com/java/technologies/javase/javase7-archive-downloads.html](https://www.oracle.com/java/technologies/javase/javase7-archive-downloads.html)

[https://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-server-jre.html](https://docs.oracle.com/javase/7/docs/webnotes/install/linux/linux-server-jre.html)