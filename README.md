# XRemoteRaspi on Ubuntu Bionic Beaver
XRemoteRaspi is an enhanced Docker container base image derived from base image "<a href="https://hub.docker.com/_/ubuntu">Ubuntu</a>" (formerly: "<a href="https://hub.docker.com/r/balenalib/rpi-raspbian/tags">resin/rpi-raspbian</a>", now deprecated). This image allows to remote control graphical applications executed on a Raspberry Pi (ARM), e.g. JDownloader2. It can be used in combination with media center Just-Enough OS (JEOS) solutions like LibreELEC, CoreELEC to run headless applications.

The docker container might be started using:
```
docker run -it -d -v /storage/JDownloader:/root/Downloads -p 3389:3389 -p 9022:22 -p 8088:8080 -p 5901:5901 ubuntu /bin/bash
```

In general, Docker containers are designed to execute a single process. Here, a complete INIT environment is used based on RUNIT to allow starting server processes in daemon mode easily. It's a powerful alternative for SYSTEMD and Sys-V-INIT scripts. Here RUNIT is used to launch:
- Xvnc
- openbox
- Tomcat (with its web application "guacamole")
- XRDP

Set a VNC password for the user that is used to launch Xvnc, here "nobody". Important: confirm setting a view-only password when the following message appears "*Would you like to enter a view-only password (y/n)?*" Otherwise the VNC authentication fails"
```
vncpasswd
```

The services are located below "/etc/service" where they can be started from via:
- runsvdir
- runsv

Ideally using "runsvdir" to load all runit services located and specified in directory "/etc/service"
```
runsvdir /etc/service &
```

Alternatively, the configured runit services can be instantiated individually using:
```
runsv /etc/service/<service_name> &
```

Due to performance problems with OpenJDK in combination with JDownloader2 the commercial flavor of Oracle JDK has been taken. To start JDownloader manually please issue the following command:
```
<jdk_install_dir>/jre/bin/java -jar /opt/jd2/JDownloader.jar
```

# Hints
- Committing the container to a new image during the installation leads to errors as a committed container becomes a read-only image layer. This results in problems when tools want to remove folders (e.g. JDownloader during an update run). That's why it's recommended to not commit a container to a new image.
