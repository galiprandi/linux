# Instalación de Arch en Gnome Boxes (Sin UEFI)

Basada en las guias:
https://wiki.archlinux.org/index.php/Installation_guide_(Espa%C3%B1ol)

## Crear la máquina virtual y bootear con la ISO de Arch

## Configurar teclado en latam/es según corresponda

~~~
loadkeys es
~~~

## Particionar el Disco

~~~
cfdisk
~~~

#### dos -> New -> [ENTER] -> primary -> Bootable -> Write -> "yes" -> Quit


## Formatear la partición /

~~~
fdisk -l
mkfs.ext4 /dev/vda1
~~~

## Montar la partición

~~~
mount /dev/vda1 /mnt
~~~

## Chequear conexión a Internet

~~~
ip link
ping  google.com [CTRL+C]
~~~

## Instalar sistema base y utilidades necesarias y crear fstab

~~~
pacstrap /mnt base linux linux-firmware grub os-prober ntfs-3g networkmanager xdg-user-dirs nano dhcpcd
genfstab -U /mnt >> /mnt/etc/fstab
~~~

## Entrar en la nueva instalación

~~~
arch-chroot /mnt
~~~

## Varias configuraciones requeridas

~~~
echo Arch > /etc/hostname

ln -sf /usr/share/zoneinfo/America/Argentina /etc/localtime

echo es_AR.UTF-8 UTF-8 >> /etc/locale.gen

echo LANG=es_AR.UTF-8 > /etc/locale.conf

echo KEYMAP=es > /etc/vconsole.conf

locale-gen

hwclock -w

~~~

## Instalar Grub

~~~
grub-install /dev/vda
grub-mkconfig -o /boot/grub/grub.cfg
~~~

## Initramfs

~~~
mkinitcpio -P
~~~

## Establecer contraseña de root y crear usuario

~~~
passwd
useradd -m -g users -G audio,lp,optical,storage,video,wheel,games,power,scanner -s /bin/bash usuario
passwd usuario
~~~

## Salir y reiniciar el sistema

~~~
exit
umount -R /mnt
shutdown now
~~~

## Luego del primer arranque existoso ingresar como root

~~~
systemctl start NetworkManager
systemctl enable NetworkManager
ping -c 3 google.com # Chequear que haya internet 
~~~

## Instalar Gnome

~~~
pacman -S gdm gnome gnome-tweak-tool
systemctl enable gdm
reboot
~~~

