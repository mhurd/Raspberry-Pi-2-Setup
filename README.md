## Debian Jessie on Raspberry Pi2

Get it from [here](https://www.collabora.com/about-us/blog/2015/02/03/debian-jessie-on-raspberry-pi-2/)

### as _root_ (basic setup on wired connection)

```bash
passwd root
adduser your-non-root-user
```

```bash
sudo vim /etc/hostname
```

```bash
adduser your-non-root-user
```

```bash
dpkg-reconfigure locales
```

```bash
apt-get install aptitude
aptitude update
aptitude safe-upgrade
```

```bash
aptitude install sudo
aptitude install vim
aptitude install locate
```
### as _your-non-root-user_

```bash
ssh-keygen -t rsa -C "your_email@example.com"
eval `ssh-agent -s
ssh-add ~/.ssh/id_rsa
```

### as _root_ zsh / tmux setup

Specific to my setup and uses a private repo you may want to skip this...

```bash
aptitude install zsh
aptitude install tmux
```

```bash
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

The _.dotfiles_ directory is specific to my setup and holds various config files
that can be symlinked to from the home directory for zsh, tmux etc.

```bash
git clone https://github.com/mhurd/.dotfiles.git
cd .dotfiles
git clone http://github.com/zsh-users/zsh-syntax-highlighting.git
git clone https://github.com/rupa/z.git
```

```bash
cd ~
ln -s ~/.dotfiles/.vimrc ~/.vimrc
ln -s ~/.dotfiles/.zshrc ~/.zshrc
ln -s ~/.dotfiles/.tmux.conf ~/.tmux.conf
ln -s ~/.dotfiles/pi/variables ~/.dotfiles/variables
```
Set */usr/bin/zsh* as the default shell:
```bash
chsh
```

remove all references to ruby from the configured theme as we don't have the required ruby install:

```bash
vim ~/.oh-my-zsh/themes/fino-time.zsh-theme
```

### as _root_ (wireless setup)

Assumes Edimax 150Mbps Wireless IEEE802.11b/g/n nano USB Adapter
EW-7811Un

```bash
aptitude install iw
aptitude install wireless-tools
aptitude install wpasupplicant
vim /etc/wpa_supplicant/wpa_supplicant.conf
```
Enter this configuration into the _wpa_supplicant.conf_ file:
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
key_mgmt=NONE
}

network={
ssid="your-ssid-here"
scan_ssid=1
proto=RSN
key_mgmt=WPA-PSK
pairwise=CCMP TKIP
group=CCMP TKIP
psk="your-key-here"
}
```

```bash
vim /etc/network/interfaces
```
Enter this configuration into the _interfaces_ file:
```
source-directory /etc/network/interfaces.d
auto lo
 
iface lo inet loopback

iface eth0 inet dhcp
 
allow-hotplug wlan0
iface wlan0 inet dhcp
pre-up wpa_supplicant -Dwext -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf -> B -P /var/run/wpa_supplicant.wlan0.pid
post-down killall -q wpa_supplicant
```

```bash
vim /etc/modprobe.d/8192cu.conf
```
Disable the power saving mode by adding this configuration to the _8192cu.conf_ file:
```
options 8192cu rtw_power_mgnt=0 rtw_enusbss=0
```
