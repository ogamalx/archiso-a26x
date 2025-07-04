#!/bin/bash
set -e

rm -rf ~/custom-archiso
mkdir -p ~/custom-archiso/CustomArchISO/airootfs/{etc/skel,etc/systemd/system/getty@tty1.service.d,home/arch,etc/xdg/openbox}
cd ~/custom-archiso/CustomArchISO

# packages
cat <<EOPKG > packages.x86_64
base
linux
linux-firmware
zsh
neofetch
git
repo
yay
jdk11-openjdk
android-tools
android-udev
base-devel
clang
cmake
gcc
make
python
ncurses
bc
bison
flex
dtc
libelf
lz4
zstd
openssl
openbox
tint2
nitrogen
xterm
kitty
rofi
xbindkeys
xorg-xmodmap
xorg-server
xorg-xinit
EOPKG

# profiledef
cat <<EOPROF > profiledef.sh
profile="CustomArchISO"
iso_label="ARCH_LIVE"
iso_publisher="Omar G"
iso_application="Arch Dev ISO for A26x Kernel/TWRP"
install_dir="arch"
buildmodes=('iso')
bootmodes=('bios.syslinux.mbr' 'uefi-boot')
arch="x86_64"
EOPROF

# autologin
cat <<EOAUTO > airootfs/etc/systemd/system/getty@tty1.service.d/autologin.conf
[Service]
ExecStart=
ExecStart=-/usr/bin/agetty --autologin arch --noclear %I \$TERM
EOAUTO

# zshrc
cat <<EOZSH > airootfs/etc/skel/.zshrc
neofetch
alias ll='ls -lah'
EOZSH

# openbox keybinds
cat <<EORC > airootfs/etc/xdg/openbox/rc.xml
<keyboard>
  <keybind key="Super_L">
    <action name="Execute"><command>rofi -show drun</command></action>
  </keybind>
  <keybind key="Super_R">
    <action name="Execute"><command>kitty</command></action>
  </keybind>
</keyboard>
EORC

mkdir -p airootfs/etc/skel/.config/openbox
cp airootfs/etc/xdg/openbox/rc.xml airootfs/etc/skel/.config/openbox/rc.xml
echo "exec openbox-session" > airootfs/etc/skel/.xinitrc

# boot menu
mkdir -p syslinux
cat <<EOBT > syslinux/archiso_sys.cfg
LABEL arch
  MENU LABEL Boot Arch (Normal)
  LINUX /arch/boot/x86_64/vmlinuz
  INITRD /arch/boot/x86_64/archiso.img
  APPEND archisobasedir=arch archiso

LABEL arch-toram
  MENU LABEL Boot Arch (RAM - toram)
  LINUX /arch/boot/x86_64/vmlinuz
  INITRD /arch/boot/x86_64/archiso.img
  APPEND archisobasedir=arch archiso toram
EOBT

# Codespace compatibility notice
if grep -q "/home/codespace" <<< "$HOME"; then
  echo "[Codespace Detected] Installing archiso dependencies..."
  sudo apt update && sudo apt install -y xorriso squashfs-tools arch-install-scripts libarchive-tools
  echo "[!] You must manually clone archiso tools if mkarchiso is not present."
fi

echo -e "\\n[✔] Setup complete. To build ISO:\\n\\n  cd ~/custom-archiso\\n  sudo mkarchiso -v CustomArchISO\\n"
