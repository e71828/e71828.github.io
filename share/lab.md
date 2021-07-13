 - begin with Alternative  Ubuntu 18.04 server 
 - uefi 500ssd + 6 TB hdd gpt
 - 1G efi; 128 GB swap;
 -GeFore RTX 2080 Ti

## proxy

`export ALL_PROXY=socks5://192.168.1.191:1080`  
` unset ALL_PROXY`

## update
```bash
sudo apt update
sudo apt dist-upgrade
sudo tasksel
#select desktop

apt-cache search linux-image
```
OR
```bash
mkdir newkernel && cd newkernel
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.0/linux-headers-5.0.0-050000_5.0.0-050000.201903032031_all.deb
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.0/linux-headers-5.0.0-050000-generic_5.0.0-050000.201903032031_amd64.deb
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.0/linux-image-unsigned-5.0.0-050000-generic_5.0.0-050000.201903032031_amd64.deb
wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v5.0/linux-modules-5.0.0-050000-generic_5.0.0-050000.201903032031_amd64.deb
dpkg -i *.deb
dpkg -l | grep linux-image
# purge  and update-grub
sudo apt purge linux-image-xx
sudo apt purge linux-headers-xx
sudo apt autoremove


sudo apt-mark showauto 
sudo apt-mark showmanual 
update-grub
dpkg -l | grep -E 'linux-image-[0-9]+' | grep -Fv $(uname -r)
dpkg: warning: while removing linux-modules-4.15.0-45-generic, directory '/lib/modules/4.15.0-45-generic' not empty so not removed


uname -r
sudo apt-mark hold linux-image-generic linux-headers-generic
#sudo apt-mark unhold linux-image-generic linux-headers-generic

lsmod | grep nouveau
```

## To disable GUI on boot, run:
```bash
sudo systemctl set-default multi-user.target
#  To enable GUI again issue the command:
sudo systemctl set-default graphical.target
#  To start Gnome session on a system without a current GUI just execute:
cat /etc/X11/default-display-manager
sudo systemctl start gdm3.service
sudo systemctl start lightdm.service 
sudo dpkg-reconfigure lightdm
sudo init 3

sudo gedit /etc/modprobe.d/blacklist.conf
	blacklist nouveau 
	blacklist intel 
	options nouveau modeset=0 

	# for nvidia display device install
	blacklist vga16fb
	blacklist nouveau
	blacklist rivafb
	blacklist rivatv
	blacklist nvidiafb

# additional
sudo vi /etc/default/grub

	# Stops the ubuntu purple screen
	#GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
	# Uncomment to disable graphical terminal (grub-pc only) 
	GRUB_TERMINAL=console

sudo update-grub
sudo reboot
```

## Install the NVIDIA display driver
>				sudo apt-get remove --purge '^nvidia.*'
>				sudo add-apt-repository ppa:graphics-drivers/ppa
>				sudo apt-get install dkms build-essential
>				sudo apt-get update
>				sudo apt-get install nvidia-430
>				sudo shutdown -r now

OR
```bash
mkdir ~/NVIDIA && cd ~/NVIDIA
wget http://us.download.nvidia.com/XFree86/Linux-x86_64/430.40/NVIDIA-Linux-x86_64-430.40.run
# Remove Nouveau driver
sudo apt purge remove xserver-xorg-video-nouveau
# Remove previously installed NVIDIA driver
sudo apt purge *nvidia*

# Execute file and answer the questions during installation
sudo chmod +x NVIDIA-Linux-x86_64-430.40.run
sudo ./NVIDIA-Linux-x86_64-430.40.run

# Reboot Ubuntu
sudo reboot

# To check if installation is successful
nvidia-smi
# Check again
lsmod | grep nouveau  # should be zero output
lsmod | grep nvidia   # should be non-zero output

# Another check. {tab} means you should press <Tab> button on your keyboard.
cat /proc/driver/nvidia/gpus/{tab}/information
```

## NetworkManager
```bash
cd /etc/netplan
sudo cp 01-netcfg.yaml 01-netcfg.yaml-backup
sudo vim 01-netcfg.yaml
	# This file describes the network interfaces available on your system
	# For more information, see netplan(5).
	network:
	  version: 2
	  renderer: NetworkManager
sudo netplan generate
sudo netplan apply
sudo systemctl restart NetworkManager.service

nmcli con show
sudo nmcli con mod "Wired connection 1"\
  ipv4.addresses "192.168.1.2/24"\
  ipv4.gateway "192.168.1.1"\
  ipv4.dns "114.114.114.114,8.8.8.8"\
  ipv4.dns-search "192.168.1.1"\
  ipv4.method "manual"
sudo netplan try
sudo nmcli con mod "Wired connection 1"\
  ipv4.addresses ""\
  ipv4.gateway ""\
  ipv4.dns ""\
  ipv4.dns-search ""\
  ipv4.method "auto"
sudo netplan try
```
### All in file NOW
```bash
amax@lab:~$ cat /etc/netplan/01-netcfg.yaml
# This file describes the network interfaces available on your system
# For more information, see netplan(5).
network:
    version: 2
    renderer: NetworkManager
    ethernets:
        eno1:
            dhcp4: no
            dhcp6: yes
            addresses:
                - 192.168.31.163/24
            gateway4: 192.168.31.1
            nameservers:
                addresses: [192.168.31.1, 114.114.114.114]
```


## Installing CUDA library for all users
```bash
wget http://developer.download.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda_10.1.243_418.87.00_linux.run
sudo apt-get install g++ freeglut3-dev build-essential libx11-dev \
    libxmu-dev libxi-dev libglu1-mesa libglu1-mesa-dev
sudo chmod +x cuda_10.1.243_418.87.00_linux.run
sudo sh cuda_10.1.243_418.87.00_linux.run
```

## To configure the CUDA environment for all users
```bash
# Create file cuda.sh
sudo touch /etc/profile.d/cuda.sh
# Open cuda.sh file
sudo vim /etc/profile.d/cuda.sh
# Add content to the file
export PATH=$PATH:/usr/local/cuda/bin
export CUDADIR=/usr/local/cuda

# Also create file cuda.conf
sudo touch /etc/ld.so.conf.d/cuda.conf
# Open cuda.conf file
sudo vim /etc/ld.so.conf.d/cuda.conf
# Add content to the file
/usr/local/cuda/lib64

# Restart ldconfig
sudo ldconfig
source /etc/profile.d/cuda.sh

# Create symbolic links to GCC7 in the CUDA bin folder.
sudo ln -s /usr/bin/gcc-7 /usr/local/cuda-10.0/bin/gcc
sudo ln -s /usr/bin/g++-7 /usr/local/cuda-10.0/bin/g++

# Test CUDA by building the examples
# Copy the CUDA samples source directory to someplace in your home directory
# Go to the directory with the samples and run:
make -j4
# There could be compilation error for the samples
# Error: cannot find -lGL
# I was able to fix it by following the instructions in this link:
# http://techtidings.blogspot.com/2012/01/problem-with-libglso-on-64-bit-ubuntu.html (the final two commands)
sudo rm /usr/lib/x86_64-linux-gnu/libGL.so
sudo ln -s /usr/lib/libGL.so.1 /usr/lib/x86_64-linux-gnu/libGL.so
```

Downgrade to cuda-10.0  
`ll /usr/local/cuda`

## user
```bash
sudo groupadd BI    
sudo useradd -m AA -g BI -s /bin/bash      
sudo passwd AA  
usermod -s /bin/bash sunweitao
grep sunweitao /etc/passwd

find / -group 1008 -exec chgrp -h guqingyue {} \;
```

## MATLAB
```bash
sudo mkdir /mnt/dest
#To turn off disk device`s write protect, we use the low level system utility hdparm like this:
sudo hdparm -r0  /dev/sdd
sudo mkdir /opt/MATLAB
sudo mkdir /opt/MATLAB/R2018b
sudo chmod 777 /opt/MATLAB/R2018b
sudo mount -o loop /media/muriuki/Transcend/MATLABxxxxxxLinux/R2018a_glnxa64_dvd1.iso /mnt/dest/
/mnt/dest/install
sudo umount /mnt/dest/
sudo mount -o loop /media/muriuki/Transcend/MATLABxxxxxxLinux/R2018a_glnxa64_dvd2.iso /mnt/dest/
sudo umount /mnt/dest/

sudo vim /usr/share/applications/matlab.desktop
#!/usr/bin/env xdg-open
[Desktop Entry]
Type=Application
Icon=/usr/share/icons/matlab.png
Name=MATLAB R2018b
Comment=Start MATLAB - The Language of Technical Computing
Exec=/opt/MATLAB/R2018b/bin/matlab -desktop -useStartupFolderPref 
Categories=Development;

sudo wget http://upload.wikimedia.org/wikipedia/commons/2/21/Matlab_Logo.png -O /usr/share/icons/matlab.png
```

## run matlab
```bash
cd ~/ana_link/
echo "exit()" >> Linkage_analysis.m
nohup matlab -nodisplay -nosplash <Linkage_analysis.m


//////sed -i '$d' fileName
```

## shadowsocks
```bash
##if Ubuntu 1804, ignore this 
sudo apt-get install software-properties-common -y
sudo add-apt-repository ppa:max-c-lv/shadowsocks-libev -y
##if Ubuntu 1804, ignore the first two lines 
sudo apt update
sudo apt install shadowsocks-libev
# Edit the configuration file

sudo tee  /etc/shadowsocks-libev/config.json > /dev/null <<EOF
{
    "server":"jp-40.fyvvv.com",
    "server_port":20311,
    "local_port":1080,
    "password":"qeq04JtC59",
    "timeout":300,
    "method":"rc4-md5"
}
EOF

# Start the service
sudo systemctl start shadowsocks-libev 
# Start the local
sudo systemctl start shadowsocks-libev-local@config.service
sudo systemctl enable shadowsocks-libev-local@config.service
sudo systemctl status shadowsocks-libev-local@config.service
# test
curl --socks5-hostname localhost:1080 ip.gs
# personal settings
sudo tee -a /etc/profile >  /dev/null <<EOF
alias useproxy="export ALL_PROXY=socks5://127.0.0.1:1080"
alias unproxy="unset ALL_PROXY"
alias ipc="curl ip.gs"
EOF

source ~/.bashrc

##obfs
sudo systemctl stop shadowsocks-libev-local@config.service
sudo apt-get install --no-install-recommends build-essential autoconf libtool libssl-dev libpcre3-dev libc-ares-dev libev-dev asciidoc xmlto automake git
git clone https://github.com/shadowsocks/simple-obfs.git
cd simple-obfs
sudo git submodule update --init --recursive
sudo ./autogen.sh
sudo ./configure &&sudo make
sudo make install
cd ..
rm -rf simple-obfs

sudo systemctl stop shadowsocks-libev-local@config.service
ss-local -c /etc/shadowsocks-libev/config.json --plugin obfs-local --plugin-opts "obfs=http;obfs-host=www.bing.com"
sudo systemctl start shadowsocks-libev-local@config.service
sudo systemctl status shadowsocks-libev-local@config.service
```


ERROR: Unable to load info from any available system
```bash
sudo apt install nvidia-prime
sudo rm -v /etc/X11/xorg.conf*
nvidia-xconfig
sudo prime-select nvidia
```

##  Google Pinyin
```bash
sudo apt-get install fcitx-googlepinyin
fcitx-config-gtk3
```

## vncserver
```bash
sudo apt install tightvncserver

vncserver :100 
vncserver -kill :100

tee  ~/.vnc/xstartup > /dev/null <<EOF
#!/bin/sh
# Uncomment the following two lines for normal desktop:
# unset SESSION_MANAGER
# exec /etc/X11/xinit/xinitrc
unset DBUS_SESSION_BUS_ADDRESS
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &
#vncconfig -iconic -nowin &
#x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
x-window-manager &
export GTK_IM_MODULE="fcitx"
export QT_IM_MODULE="fcitx"
export XMODIFIERS="@im=fcitx"
fcitx&
mate-session &
EOF

vncserver :100 
vncserver -kill :100
vncpasswd
sudo netstat -lp|grep -i vnc
sudo rm  -rf /tmp/.X*
vncserver -geometry 1680x1024 :99

##xrdp
sudo apt-get install xrdp 
sudo apt-get install mate-core mate-desktop-environment mate-notification-daemon
sudo sed -i.bak '/fi/a #xrdp multiple users configuration \n mate-session \n' /etc/xrdp/startwm.sh
sudo sed -i '/fi/a mate-session \n export GTK_IM_MODULE="fcitx" \n export QT_IM_MODULE="fcitx" \n export XMODIFIERS="@im=fcitx"\n fcitx \n' /etc/xrdp/startwm.sh
sudo /etc/init.d/xrdp restart
sudo netstat -lp |grep -i xrdp
```
`ps aux|grep <user>`

##  NDownload cuDNN v7.6.2 (July 22, 2019), for CUDA 10.1
```bash
# first get and upload
 tar -zxvf cudnn-10.1-linux-x64-v7.6.2.24.tgz
sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

sudo dpkg -i libcudnn7_7.6.2.24-1+cuda10.1_amd64.deb
sudo dpkg -i libcudnn7-dev_7.6.2.24-1+cuda10.1_amd64.deb
sudo dpkg -i libcudnn7-doc_7.6.2.24-1+cuda10.1_amd64.deb
 
cp -r /usr/src/cudnn_samples_v7/ $HOME
cd  $HOME/cudnn_samples_v7/mnistCUDNN
make clean && make
./mnistCUDNN
```

## Installing Anaconda3
```bash
sudo bash Anaconda3-2019.07-Linux-x86_64.sh
	opt/anaconda3
source ~/.bashrc
conda config --set auto_activate_base False 
anaconda-navigator
cat ~/.bashrc
sudo chmod 777 -R /opt/anaconda3
conda update conda

sudo tee -a /etc/profile >  /dev/null <<EOF
# >>> conda initialize >>>
# !! Contents within this block are managed by 'conda init' !!
__conda_setup="$('/opt/anaconda3/bin/conda' 'shell.bash' 'hook' 2> /dev/null)"
if [ $? -eq 0 ]; then
    eval "$__conda_setup"
else
    if [ -f "/opt/anaconda3/etc/profile.d/conda.sh" ]; then
        . "/opt/anaconda3/etc/profile.d/conda.sh"
    else
        export PATH="/opt/anaconda3/bin:$PATH"
    fi
fi
unset __conda_setup
# <<< conda initialize <<<
EOF

source /etc/profile
anaconda-navigator
conda --version
conda list

conda info -e  
# conda info --envs
```

## nvidia-docker 2.0
```bash
## Add the package repositories
distribution=$(. /etc/os-release;echo $ID$VERSION_ID)
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | sudo apt-key add -
curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update && sudo apt-get install -y nvidia-container-toolkit
sudo systemctl restart docker
sudo apt-get install nvidia-docker2
sudo pkill -SIGHUP dockerd
sudo nvidia-docker run --rm hello-world
docker run --runtime=nvidia --rm nvidia/cuda nvidia-smi
nvidia-docker run --rm -ti nvidia/cuda nvcc --version
nvidia-docker run --name digits --rm -ti -p 8000:34448 nvidia/digits
sudo ln /dev/null /dev/raw1394
```

## NumLock on by default
```bash
sudo apt install numlockx

sudo tee -a /etc/lightdm/lightdm.conf >  /dev/null <<EOF
greeter-setup-script=/usr/bin/numlockx on
EOF
```

## share files
```bash
sudo apt install samba
sudo chown -R nobody.nogroup the_folder
sudo chmod -R 777 the_folder
sudo -u nobody touch test_file
sudo vim /etc/samba/smb.conf

[share_name]              ;the share name can be what ever you want
browseable = yes
path = the_complete_path_to_the_shared_folder
guest ok = yes
read only = no
create mask = 777
directory mask = 777

testparm
sudo service smbd restart
sudo systemctl restart nmbd
```

## share codes
```
sudo usermod -a -G sambashare guqingyue
sudo chgrp -R sambashare Resource/
sudo chmod g+sw Resource/


sudo journalctl _COMM=sudo
```

## clash
```bash
#cat /etc/systemd/system/clash.service
[Unit]
Description=A rule-based tunnel in go
After=network-online.target
[Service]
Type=simple
User=amax
Group=amax
UMask=007
ExecStart=/home/amax/Documents/go/clash
Terminal=false
Restart=on-failure
# Configures the time to wait before service is stopped forcefully.
TimeoutStopSec=300
[Install]
WantedBy=multi-user.target

amax@lab:/etc$ ls /usr/share/applications/Clash.desktop
/usr/share/applications/Clash.desktop
amax@lab:/etc$ cat  /usr/share/applications/Clash.desktop
[Desktop Entry]
Type=Application
Name=Clash
Comment=A rule-based tunnel in go
Exec=/home/amax/Documents/go/clash
Icon=/usr/share/icons/clash.png
Terminal=false
Categories=Development;

amax@lab:~/.config/clash$ sudo systemctl disable clash
amax@lab:~/.config/clash$ git clone --depth=1 --branch=gh-pages https://github.com/Dreamacro/clash.git
amax@lab:~/.config/clash$ ln -s clash-dashboard/ ui/
amax@lab:~/.config/clash$ grep external config.yaml
external-controller: 0.0.0.0:9080
external-ui: /ui

amax@lab:~ $ docker run -d --name clash-client --restart always -p 7890:7890 -p 7891:7891 -p 9080:9080 -v /home/amax/.config/clash/config.yaml:/root/.config/clash/config.yaml -v /home/amax/.config/clash/ui:/ui dreamacro/clash
```

## deluge
```bash
amax@lab:~$ cat /etc/systemd/system/deluged.service
[Unit]
Description=Deluge Bittorrent Client Daemon
After=network-online.target
[Service]
Type=simple
User=deluge
Group=sambashare
UMask=002
ExecStart=/usr/bin/deluged -d
Restart=on-failure
# Configures the time to wait before service is stopped forcefully.
TimeoutStopSec=300
[Install]
WantedBy=multi-user.target
amax@lab:~$ cat /etc/systemd/system/deluge-web.service
[Unit]
Description=Deluge Bittorrent Client Web Interface
After=network-online.target
[Service]
Type=simple
User=deluge
Group=sambashare
UMask=027
ExecStart=/usr/bin/deluge-web
Restart=on-failure
[Install]
WantedBy=multi-user.target
amax@lab:~$
```

## clash subscribe
```bash
amax@lab:~/.config/clash$ cat Scheduled.sh
#! /bin/bash
echo "yes"
cd /home/amax/.config/clash
wget -O config.yaml https://yoyu.cc/subscribe/8171/**********/clash
sed -i '/^\s*external-controller/ c\external-controller: 0.0.0.0:9080' config.yaml
sed -i '/^\s*external-controller/ a\external-ui: /ui' config.yaml
docker restart clash-client
```
`crontab -l` display
`0 21 * * * /home/amax/.config/clash/Scheduled.sh`

Clash Webui: [`lab:9080/ui`](http://192.168.31.163:9080/ui)`



## nvidia-450 update
`sudo service lightdm stop && sudo ./NVIDIA-Linux-x86_64-450.80.02.run  --no-cc-version-check`
[参考](https://askubuntu.com/questions/1143303/dkms-failure-because-gcc-version-is-newer-than-that-used-to-compile-kernel)  

## GCC learn
`ll /usr/bin/gcc*`
