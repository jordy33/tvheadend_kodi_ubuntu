## How to install tvheadend in ubuntu

### Install Tvheadend
Update
```
apt-get update
apt-get upgrade
```
###Language
```
First run locale to list what locales currently defined for the current user account:

$ locale

Then generate the missing locale and reconfigure locales to take notice:

$ sudo locale-gen "en_US.UTF-8"
Generating locales...
  en_US.UTF-8... done
Generation complete.

$ sudo dpkg-reconfigure locales
Generating locales...
  en_US.UTF-8... up-to-date
Generation complete.

Nothing suggested above worked in my case (Ubuntu Server 12.04LTS). What finally helped was putting to the file /etc/environment:

LC_ALL=en_US.UTF-8
LANG=en_US.UTF-8
```
Dependencies

```
sudo apt-get install gitk
sudo apt-get install libiconv
sudo apt-get install libic6
sudo apt-get install libc6
sudo apt-get install libiconv-dev
sudo apt-get install libc6-dev
sudo apt-get install clang
sudo apt-get install libdvbcsa-dev
sudo apt-get install dvb-apps
git clone https://github.com/tvheadend/dtv-scan-tables.git
cd dtv-scan-tables/
make clean
sudo make dvbv3
sudo make install

git clone https://github.com/tvheadend/tvheadend.git
gitk --all
git checkout 9874ab0b1d4a6752840a9a23bf7502c3e623825f
```

### Configure Options
```
 ./configure --enable-dvbcsa --enable-constcw  --enable-bundle
```

### Package
```
AUTOBUILD_CONFIGURE_EXTRA="--enable-dvbcsa --enable-constcw  --enable-bundle" ./Autobuild.sh -t xenial-amd64
```

### Compile
```
make
```

### Test

```
./build.linux/tvheadend
```
### Install Kodi
```
sudo apt-get install software-properties-common
sudo add-apt-repository ppa:team-xbmc/ppa
sudo apt-get update
sudo apt-get install kodi
```

### Install hts tvheadend client
```
sudo apt-get install kodi-pvr-hts
sudo apt-get install kodi-pvr-iptvsimple
```

### Install FFmpeg
```
sudo apt-get install ffmpeg
ffmpeg -version
```

In Kodi, input user admin and password 
Or in TVH change in General/Base/Authentication type= Plain Insecure:
In tvh add an IPTV network (no automatic).  Add a mux for that network and use a URL like this....

```
pipe:///usr/bin/ffmpeg -loglevel fatal -re -i http://admin:trinity@192.168.1.79:9981/stream/channel/add03d46c01d987cf950098e621256b1?ticket=817e1591bb28cac007edcacdb100fb24628c25bb -c:v copy -filter_complex [0:1][0:2][0:3]amerge=inputs=3,pan=5.1|FL=c0|FR=c1|FC=c2|LFE=c3|BL=c4|BR=c5 -c:a eac3 -f mpegts pipe:1
```

(Get the http link openning the link to open the channel)

It'll scan in a new service and you just map that to a new channel.  

### Compile/Install OSCAM-EMU

```
mkdir oscam
cd oscam
git clone https://github.com/oscam-emu/oscam-patched
cd oscam-patched
git checkout oscam11505-emu791
```

Select all protocols and all options
```
make config
```

### Compile oscam
```
make
cd Distribution
mv oscam-1.20_svn11505-791-x86_64-linux-gnu-ssl ./oscam
sudo cp ./oscam /usr/local/bin/oscam
```

### Create Directory: 

```
/home/jordy/.oscam/tmp
```

### Create a directory in: /home/jordy/.oscam and insert the following files:
oscam.conf
```
# oscam.conf generated automatically by Streamboard OSCAM 1.20-unstable_svn SVN r11392
# Read more: http://www.streamboard.tv/svn/oscam/trunk/Distribution/doc/txt/oscam.conf.txt

[global]
logfile                       = stdout
fallbacktimeout               = 3
bindwait                      = 5
initial_debuglevel            = 64
nice                          = -1
maxlogsize                    = 200
waitforcards                  = 0
preferlocalcards              = 1
lb_mode                       = 1
lb_save                       = 100
lb_savepath                   = /home/jordy/.oscam/oscam.stat
lb_auto_timeout_t             = 26000

[cache]

[streamrelay]
stream_relay_enabled          = 0

[dvbapi]
enabled                       = 1
au                            = 1
pmt_mode                      = 4
listen_port                   = 9001
delayer                       = 60
ecminfo_type                  = 4
user                          = tvheadend
read_sdt                      = 1
extended_cw_api               = 1
boxtype                       = pc

[webif]
httpport                      = 8888
httprefresh                   = 2
httppollrefresh               = 2
httpallowed                   = 127.0.0.1,192.168.0.0-192.168.254.254
```

oscam.server

```
[reader]
label                         = emulator
protocol                      = emu
device                        = emulator
caid                          = 090F,0500,1801,0604,2600,FFFF,0E00,4AE1,1010
detect                        = cd
disablecrccws_only_for        = 0E00:000000
ident                         = 0D00:0000C0;0D02:0000A0;0500:023800,021110,007400;0E00:000000;1801:000000;1010:000000
group                         = 1
emmcache                      = 2,0,2,0
emu_auproviders               = 0E00:000000
auprovid                      = 000E00

[reader]
label                         = SoftCam.Key
protocol                      = constcw
device                        = /home/jordy/.oscam/SoftCam.Key
caid                          = 0D00,0D02,090F,0500,1801,0604,2600,FFFF,0E00,4AE1,1010
ident                         = 1801:000000,001101;0604:000000;2600:000000;FFFF:000000;0E00:000000;4AE1:000000;1010:000000
group                         = 1
```
oscam.services

```
# oscam.services generated automatically by Streamboard OSCAM 1.20_svn SVN r11441
# Read more: http://www.streamboard.tv/svn/oscam/trunk/Distribution/doc/txt/oscam.services.txt

[afn]
caid                          = 0E00
provid                        = 000000
srvid                         = 0004,0005,0006,0007,0008,0009,012C,0098,0096,04B1,03E9,04B2,00AD,00B7,00B9,00AF,019E,0193,019D,0192,0191,004A,004C,009D,00A0,004B,0097,000F,0047,0048,0003
```
oscam.user

```
[account]
user                          = tvheadend
pwd                           = tvheadend
monlevel                      = 4
au                            = 1
group                         = 1,2,3,4,5,6,7,8,9,11,12,13,14,15,16,17,18,19,21,22,23,24,25,26,27,28,29,31,32,33,34,35,36,37,38
```

Execute to test:

```
oscam 
```

Create Deamon at run boot
```
sudo apt-get install vim
sudo vim /etc/init.d/oscam
```
Insert the following code

```
#!/bin/sh
### BEGIN INIT INFO
# Provides: oscam
# Required-Start: $local_fs $network $remote_fs
# Required-Stop: $local_fs $network $remote_fs
# Default-Start:  2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: start and stop service oscam
# Description: oscam
### END INIT INFO

DAEMON=/usr/local/bin/oscam
PIDFILE=/var/run/oscam.pid
DAEMON_OPTS="-p 4096 -r 2 -B ${PIDFILE} -c /home/jordy/.oscam -t /home/jordy/.oscam/tmp"

test -x ${DAEMON} || exit 0

. /lib/lsb/init-functions

case "$1" in
  start)
    log_daemon_msg "Starting OScam..."
    /sbin/start-stop-daemon --start --quiet --background --name oscam --exec ${DAEMON} -- ${DAEMON_OPTS}
    log_end_msg $?
    ;;
  stop)
    log_daemon_msg "Stopping OScam..."
    /sbin/start-stop-daemon -R 5 --stop --name oscam --exec ${DAEMON}
    log_end_msg $?
    ;;
  restart)
    $0 stop
    $0 start
    ;;
  force-reload)
    $0 stop
    /bin/kill -9 `pidof oscam`
    /usr/bin/killall -9 oscam
    $0 start
    ;;
  *)
    echo "Usage: /etc/init.d/oscam {start|stop|restart|force-reload}"
    exit 1
    ;;
esac
```
To “enable” the script and the starting up of oscam on reboot, use the following commands on Ubuntu/Debian:
```
sudo chmod +x /etc/init.d/oscam
sudo update-rc.d oscam defaults
```
To force detect service
```
sudo systemctl daemon-reload
sudo systemctl enable oscam
sudo systemctl start oscam
```

Enter Tvheadend http://192.168.1.91:9981
```
Configuration/General/Base:User interfase level: Expert
(Cas) Menu should appear
```

