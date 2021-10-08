# Raspberry
## Raspberry Pi OS Lite
- 2021-03-04-raspios-buster-armhf-lite.zip
- Total size:	441 MB				

## Monitor
## raspi-config
- System:
    - auotlogin console
    - wireless Lan
    - passwd
    - hostname
    - network at boot
- Interface:
    - ssh
    - I2C
    - Remote GPIO
- Localisation:
    - timezone: UTC+8
    - keyboard: 104, other, US, default

## Upload key

## I2C
- sudo apt-get install -y i2c-tools
- sudo i2cdetect -l
- sudo i2cdetect -y 1
- sudo i2cdump -y 1
- sudo apt install -y python3-pip && pip3  install smbus

# Ubuntu server 20.10
```
$ cat /etc/netplan/50-cloud-init.yaml
network:
    ethernets:
        eth0:
            dhcp4: true
            optional: true
    version: 2
    wifis:
        wlan0:
            dhcp4: no
            addresses: 
              - 192.168.31.15/24
            gateway4: 192.168.31.1
            optional: true
            access-points:
                "intellisense_lab2.4\"\n  scan_ssid=1\n# \"hack!":
                    password: "zngzsys12345678"
```
`sudo netplan try`

`sudo netplan apply`

## Autologon
```bash
$ sudo systemctl edit getty@tty1.service
[Service]
ExecStart=
ExecStart=-/sbin/agetty --autologin pi --noclear %I $TERM
```



## Disabling on-board Bluetooth and Using ttyAMA0
```bash
sudo systemctl status serial-getty@ttyAMA0.service
sudo systemctl disable serial-getty@ttyAMA0.service
sudo systemctl status serial-getty@ttyS0.service
sudo systemctl disable serial-getty@ttyS0.service

tail -n 6 /boot/firmware/config.txt
# Disable Bluetooth added by e71828,20210512
#dtoverlay=disable-bt

# Switches the Bluetooth function to use the mini UART
dtoverlay=miniuart-bt
force_turbo=1
```

`sudo usermod -aG dialout $USER`
### Hit any key to stop autoboot: 2 1 0
open serial: manage to enter `uboot`, `setenv bootdelay -2`, `saveenv`

## [Rasp Gpio](../pic/GPIO-Pinout-Diagram-2.png)
- PWM (pulse-width modulation)
    - Software PWM available on all pins
    - Hardware PWM available on GPIO12, GPIO13, GPIO18, GPIO19
- SPI
    - SPI0: MOSI (GPIO10); MISO (GPIO9); SCLK (GPIO11); CE0 (GPIO8), CE1 (GPIO7)
    - SPI1: MOSI (GPIO20); MISO (GPIO19); SCLK (GPIO21); CE0 (GPIO18); CE1 (GPIO17); CE2 (GPIO16)
- I2C
    - Data: (GPIO2); Clock (GPIO3)
    - EEPROM Data: (GPIO0); EEPROM Clock (GPIO1)
- Serial
    - TX (GPIO14); RX (GPIO15)


## PWM
```
sudo usermod -aG dialout $USER
sudo chown root:dialout /dev/gpiomem
sudo chmod g+rw /dev/gpiomem
./pwmSoft.py
```
 find the [`pwmSoft.py`](https://gist.github.com/e71828/f7bbf624a3f300aafe77d95ada1f08dc)


## STM32flash
```bash
wget https://udomain.dl.sourceforge.net/project/stm32flash/stm32flash-0.6.tar.gz
tar -xvpzf stm32flash-0.6.tar.gz
cd stm32flash-0.6 && make

./stm32flash -r ~/read.bin -R -i '-dtrrtsdtr,:-dtr-rts' /dev/ttyUSB0
./stm32flash -w ~/LED.hex -R -i '-dtrrtsdtr,:-dtr-rts' /dev/ttyUSB0

sudo make install
```
```bash
sudo apt isntall stm32flash
stm32flash -w ~/LED.hex -R -i '-dtr,rts,dtr,:-dtr,-rts' /dev/ttyUSB0
```

## STM32Debug
```bash
# build
git clone https://github.com/openocd-org/openocd.git --depth=1
cd openocd && ./bootstrap
./configure --enable-ftdi --enable-sysfsgpio --enable-bcm2835gpio
make -j4
sudo make install
```

```
# test
ubuntu@ubuntu:~/openocd -f interface/raspberrypi-native.cfg -f target/stm32f1x.cfg -c "program USART.axf verify reset exit"
ubuntu@ubuntu:~/openocd$ sudo openocd -f interface/stlink.cfg -f target/stm32f1x.cfg -c "program USART.axf verify reset exit"
ubuntu@ubuntu:/usr/local/share/openocd/scripts/interface$ cat openocd.cfg
source [find stlink.cfg]
transport select hla_swd
bindto 0.0.0.0
set WORKAREASIZE 0x2000
#type of STM32
source [find ../target/stm32f1x.cfg]
reset_config none
#srst_nogate
adapter srst delay 100
adapter srst pulse_width 100
init
targets
reset hal
ubuntu@ubuntu:/usr/local/share/openocd/scripts/interface$ sudo openocd
Open On-Chip Debugger 0.11.0+dev-g9188115 (2021-10-08-13:05)
Licensed under GNU GPL v2
For bug reports, read
        http://openocd.org/doc/doxygen/bugs.html
Info : The selected transport took over low-level target control. The results might differ compared to plain JTAG/SWD
Info : DEPRECATED target event trace-config; use TPIU events {pre,post}-{enable,disable}
Info : clock speed 1000 kHz
Info : STLINK V2J37S7 (API v2) VID:PID 0483:3748
Info : Target voltage: 3.265209
Error: init mode failed (unable to connect to the target)


Info : Listening on port 6666 for tcl connections
Info : Listening on port 4444 for telnet connections

```
