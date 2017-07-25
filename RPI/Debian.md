## Install guide for RPI Debian 8

Default login
```
Username: pi
Password: raspberry
```

```
sudo su
passwd
```

Set up network on eth0

/etc/network/interfaces
```
auto eth0
iface eth0 inet dhcp
```

Install tools
```
apt-get update
apt-get upgrade
apt-get install zsh vim tmux htop
```

- Install dotfiles

- Run raspi-config
 - Boot Options
  - Console
 - Internationalisation Options
  - Change Locale
   - nb_NO.UTF-8 UTF-8 
   - Default: en_GB.UTF-8
  - Change Keyboard Layout
   - Default, Norwegian
 - Change timezone
  - Europe/Oslo

Configure SSH
PermitRootLogin yes

userdel -r pi

vim /etc/hostname

# Wifi

/etc/wpa_supplicant/wpa_supplicant.conf

```
network={
	ssid="testing"
	psk="testingPassword"
}
```
