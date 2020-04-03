# Guide for setup linux fedora 31 server CS 1.6
  
# Permissive selinux 
```sh
sudo vim /etc/selinux/config 
SELINUX=permissive 
```
  
# Firewalld
```sh
sudo systemctl enable --now firewalld
sudo firewall-cmd --zone=public --add-port=27015/udp --permanent
sudo firewall-cmd --zone=public --add-port=27020/tcp --permanent
sudo firewall-cmd --reload
```
  
# Update and install packages 
```sh
sudo dnf update -y 
sudo shutdown -r now 
```
  
# Setup SteamCMD Linux (https://developer.valvesoftware.com/wiki/SteamCMD:ru) 
```sh
sudo dnf install wget glibc libstdc++ glibc.i686 libstdc++.i686
sudo useradd steam
sudo passwd steam
sudo usermod -a -G wheel steam
sudo -u steam -i
mkdir ~/steamcmd && cd ~/steamcmd
wget https://steamcdn-a.akamaihd.net/client/installer/steamcmd_linux.tar.gz
tar -xvzf steamcmd_linux.tar.gz
./steamcmd.sh
<steam> exit
```
  
```sh
sudo -u steam -i
sudo mkdir /opt/csserver
sudo chown -R steam:steam /opt/csserver/
./steamcmd.sh
login anonymous
force_install_dir /opt/csserver
#if install error, retry 2-3 times - it is normal for valve
app_update 90 validate
exit
```
  
```sh
sudo -u steam -i
mkdir ~/.steam
ln -s ~/steamcmd/linux32 ~/.steam/sdk32
touch /opt/csserver/cstrike/listip.cfg
touch /opt/csserver/cstrike/banned.cfg
```
  
```sh
sudo vim /opt/csserver/cstrike/server.cfg
// Use this file to configure your DEDICATED server. 
// This config file is executed on server start.

// disable autoaim
sv_aim 0

// disable clients' ability to pause the server
pausable 0

// default server name. Change to "Bob's Server", etc.
hostname "IT4IT Counter-Strike 1.6 Server"

// maximum client movement speed 
sv_maxspeed 320

// 20 minute timelimit
mp_timelimit 20

mp_roundtime 3
mp_autoteambalance 0
mp_autokick 0
sv_maxrate 0

//admin password
rcon_password "enter_password"

sv_cheats 0

// load ban files
exec listip.cfg
exec banned.cfg
```
  
```sh
# Run game
sudo -u steam -i
cd /opt/csserver
./hlds_run -game cstrike -console +ip 0.0.0.0 +maxplayers 32 +map de_dust2 -secure -sv_lan 0 -autoupdate -port 27015
```
  
# Usage admin rcon in game
```sh
# Get admin
rcon_password enter_password
# Map list 
rcon maps *
# Change map 
rcon changelevel de_dust2
#Full rcon command list
https://unreal.in.ua/info/rconcomand
```