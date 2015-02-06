## Debian Jessie on Raspberry Pi2

Get it from [here](https://www.collabora.com/about-us/blog/2015/02/03/debian-jessie-on-raspberry-pi-2/)

### as _root_ (basic setup on wired connection)

```bash
passwd root
```

```bash
sudo vim /etc/hostname
```

> your-hostname

```bash
ssh-keygen -t rsa -C "your_email@example.com"
```

```bash
eval `ssh-agent -s
```

```bash
ssh-add ~/.ssh/id_rsa
```

```bash
adduser mhurd
```

```bash
dpkg-reconfigure locales
```

```bash
apt-get install aptitude
```

```bash
aptitude update
```

```bash
aptitude safe-upgrade
```

```bash
aptitude install sudo
```

```bash
aptitude install vim
```


### Zsh / tmux setup

Specific to my setup and uses a private repo you may want to skip this...

```bash
aptitude install zsh
```

```bash
git clone git://github.com/robbyrussell/oh-my-zsh.git ~/.oh-my-zsh
```

The _.dotfiles_ directory is specific to my setup and holds various config files
that can be symlinked to from the home directory for zsh, tmux etc.

```bash
git clone https://github.com/mhurd/.dotfiles.git
```

```bash
cd .dotfiles
```

```bash
git clone http://github.com/zsh-users/zsh-syntax-highlighting.git
```

```bash
git clone https://github.com/rupa/z.git
```

```bash
cd ~
```

```bash
ln -s ~/.dotfiles/.vimrc ~/.vimrc
```

```bash
ln -s ~/.dotfiles/.zshrc ~/.zshrc
```

```bash
ln -s ~/.dotfiles/.tmux.conf ~/.tmux.conf
```

```bash
ln -s ~/.dotfiles/pi/variables ~/.dotfiles/variables
```

```bash
chsh
```

remove all references to ruby from the configured theme as we don't have the required ruby install:

```bash
vim ~/.oh-my-zsh/themes/fino-time.zsh-theme
```

> /usr/bin/zsh

### as _root_ (wireless setup)

Assumes 150Mbps Wireless IEEE802.11b/g/n nano USB Adapter
EW-7811Un

```bash
aptitude install iw
```

```bash
aptitude install wireless-tools
```

```bash
aptitude install locate
```

```bash
aptitude install wpasupplicant
```

```bash
vim /etc/wpa_supplicant/wpa_supplicant.conf
```

ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
network={
key_mgmt=NONE
}

network={
ssid="your-ssid-here"
> scan_ssid=1
> proto=RSN
> key_mgmt=WPA-PSK
> pairwise=CCMP TKIP
> group=CCMP TKIP
> psk="your-key-here"
> }

```bash
vim /etc/network/interfaces
```

> source-directory /etc/network/interfaces.d
> auto lo
> 
> iface lo inet loopback
> 
> iface eth0 inet dhcp
> 
> allow-hotplug wlan0
> iface wlan0 inet dhcp
> pre-up wpa_supplicant -Dwext -i wlan0 -c /etc/wpa_supplicant/wpa_supplicant.conf -> B -P /var/run/wpa_supplicant.wlan0.pid
> post-down killall -q wpa_supplicant

```bash
vim /etc/modprobe.d/8192cu.conf
```

> options 8192cu rtw_power_mgnt=0 rtw_enusbss=0
