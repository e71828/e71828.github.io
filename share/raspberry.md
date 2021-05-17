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
                "intellisense_lab2.4":
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
