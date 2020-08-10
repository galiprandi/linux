# Instalación de Arch en Gnome Boxes (Sin UEFI)

Basada en las guias:
https://wiki.archlinux.org/index.php/Installation_guide_(Espa%C3%B1ol)
https://denovatoanovato.net/instalar-arch-linux/#creando-unidad-de-arranque

## Crear la máquina virtual y bootear con la ISO de Arch

## Configurar teclado en la/es según corresponda

~~~
loadkeys la-latin1
~~~

## Particionar el Disco

~~~
cfdisk
~~~

Crear particiones: 

- 512M Primary Boot

- Espacio disponible Primary

## Formatear la partición /

~~~
mkfs.ext2 /dev/vda1
mkfs.ext4 /dev/vda2
~~~

## Montar la partición

~~~
mount /dev/vda2 /mnt
mkdir /mnt/boot
mount /dev/vda1 /mnt/boot
~~~

## Chequear conexión a Internet

~~~
ip link
ping  google.com
~~~

## Instalar sistema base y utilidades necesarias

~~~
pacstrap /mnt base linux linux-firmware grub os-prober ntfs-3g networkmanager xdg-user-dirs nano dhcpcd intel-ucode
~~~

## Entrar en la nueva instalación

~~~
arch-chroot /mnt
~~~

## Configuración requeridas

~~~
echo Arch > /etc/hostname

ln -sf /usr/share/zoneinfo/America/Argentina /etc/localtime

nano /etc/locale.gen # descomentar es_AR.UTF-8

echo LANG=es_AR.UTF-8 > /etc/locale.conf

locale-gen

hwclock -w

echo KEYMAP=la-latin1 > /etc/vconsole.conf
~~~

## Instalar Grub

~~~
grub-install /dev/sda
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

## Luego del primer arranque existoso

~~~
systemctl start NetworkManager
systemctl enable NetworkManager
~~~

## Instalar Gnome

~~~
pacman -S gdm gnome gnome-extras gnome-shell
systemctl enable gdm
reboot
~~~

