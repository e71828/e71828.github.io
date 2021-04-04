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
