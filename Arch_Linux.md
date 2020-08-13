# Instalar Arch Linux en 15 horrendos pasos (sobre Gnome Boxes)

Basada en las guias:
https://wiki.archlinux.org/index.php/Installation_guide_(Espa%C3%B1ol)

## 1 - Crear la máquina virtual y bootear con la ISO de Arch
Descarga oficial: https://mirror.ufro.cl/archlinux/iso/2020.08.01/

## 2 - Configurar teclado en latam/es según corresponda

~~~
loadkeys la-latin1
~~~
> Especifica la distribución de teclado según el teclado que se dispone. Ej: es / en / la-latin1

## 3 - Particionar el Disco

~~~
cfdisk
~~~

> dos -> New -> [ENTER] -> primary -> Bootable -> Write -> "yes" -> Quit


## 4 - Formatear la partición /

~~~
fdisk -l
mkfs.ext4 /dev/sda1
~~~

## 5 - Montar la partición

~~~
mount /dev/vda1 /mnt
~~~

## 6 - Chequear conexión a Internet

~~~
ip link
ping -c 3 google.com [CTRL+C]
~~~

## 7 - Instalar sistema base y utilidades necesarias y crear fstab

~~~
pacstrap /mnt base sudo grub networkmanager xdg-user-dirs nano
genfstab -U /mnt >> /mnt/etc/fstab
~~~

> genfstab genera el archivo /mnt/etc/fstab que contiene las unidades que deben cargarse al inicio del sistema.

## 8 - Entrar en la nueva instalación

~~~
arch-chroot /mnt
~~~

> Ingresa a la nueva instalación de Arch

## 9 - Varias configuraciones requeridas

~~~
ln -sf /usr/share/zoneinfo/America/Argentina /etc/localtime

hwclock --systohc

echo Arch > /etc/hostname

echo es_AR.UTF-8 UTF-8 >> /etc/locale.gen

locale-gen

echo KEYMAP=la-latin1 > /etc/vconsole.conf

echo LANG=es_AR.UTF-8 > /etc/locale.conf

echo $'127.0.0.1 localhost\n::1 localhost' >> /etc/hosts
~~~

> - Configura la zona horaria de Argentina.
> - Configura que el reloj se actualice con cada inicio.
> - Configura el idioma Español de Argentina cómo defecto del sistema.
> - Genera los archivos de idioma necesarios.
> - Define la distribución del teclado por defecto en la consola.
> - Crea el archivo /etc/locale.conf que carga el idioma en cada arranque.
> - Crea el archivo /etc/hosts con ajustes básicos.

## 10 - Instalar Grub (Gestor de arranque)

~~~
grub-install --target=i386-pc /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
~~~

## 11 - Establecer contraseña de root y crear usuario

~~~
passwd
useradd -m -g users -G audio,lp,optical,storage,video,wheel,games,power,scanner -s /bin/bash usuario
passwd usuario
echo $'%wheel ALL=(ALL) ALL' >> /etc/sudoers
~~~

> - Cambia la contraseña del usuario root.
> - Crea un nuevo usuario 'usuario' (reemplace por su usuario) y le asigna configuraciones necesarias.
> - Cambia la contraseña de nuevo usuario.
> - Permite que el nuevo usuario utilice el comando sudo (super usuario)

## 12 - Salir y reiniciar el sistema

~~~
exit
umount -R /mnt
shutdown now
~~~

## 13 - Quite el disco de instalación y inicie nuevamente

> - No olvide cruzar los dedos

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

