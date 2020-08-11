# Instalar Arch Linux en 15 horrendos pasos (sobre Gnome Boxes)

Basada en las guias:
https://wiki.archlinux.org/index.php/Installation_guide_(Espa%C3%B1ol)

## 1 - Crear la máquina virtual y bootear con la ISO de Arch
Descarga oficial: https://mirror.ufro.cl/archlinux/iso/2020.08.01/

## 2 - Configurar teclado en latam/es según corresponda

~~~
loadkeys es
~~~

## 3 - Particionar el Disco

~~~
cfdisk
~~~

#### dos -> New -> [ENTER] -> primary -> Bootable -> Write -> "yes" -> Quit


## 4 - Formatear la partición /

~~~
fdisk -l
mkfs.ext4 /dev/vda1
~~~

## 5 - Montar la partición

~~~
mount /dev/vda1 /mnt
~~~

## 6 - Chequear conexión a Internet

~~~
ip link
ping  google.com [CTRL+C]
~~~

## 7 - Instalar sistema base y utilidades necesarias y crear fstab

~~~
pacstrap /mnt base linux linux-firmware grub ntfs-3g networkmanager xdg-user-dirs nano dhcpcd
genfstab -U /mnt >> /mnt/etc/fstab
~~~

## 8 - Entrar en la nueva instalación

~~~
arch-chroot /mnt
~~~

## 9 - Varias configuraciones requeridas

~~~
echo Arch > /etc/hostname

ln -sf /usr/share/zoneinfo/America/Argentina /etc/localtime

echo es_AR.UTF-8 UTF-8 >> /etc/locale.gen

echo LANG=es_AR.UTF-8 > /etc/locale.conf

echo KEYMAP=es > /etc/vconsole.conf

locale-gen

hwclock -w

~~~

## 10 - Instalar Grub

~~~
grub-install /dev/vda
grub-mkconfig -o /boot/grub/grub.cfg
~~~

## 11 - Initramfs

~~~
mkinitcpio -P
~~~

## 12 - Establecer contraseña de root y crear usuario

~~~
passwd
useradd -m -g users -G audio,lp,optical,storage,video,wheel,games,power,scanner -s /bin/bash usuario
passwd usuario
~~~

## 13 - Salir y reiniciar el sistema

~~~
exit
umount -R /mnt
shutdown now
~~~

## 14 - Luego del primer arranque existoso ingresar como root

~~~
systemctl start NetworkManager
systemctl enable NetworkManager
ping -c 3 google.com # Chequear que haya internet 
~~~

## 15 - Instalar Gnome

~~~
pacman -S gdm gnome gnome-tweak-tool
systemctl enable gdm
reboot
~~~

