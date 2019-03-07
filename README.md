[gentoo](home.md) [instalação](inst.md)



## Carregar teclado

```css
loadkeys br-abnt2
```

## Mostrar a interface da rede
```css
ip -s -c -h a
```

## Configurar rede com wifi

```css
net-setup nomePlacaRede
```

## Se for preciso levante a placa de rede com

```css
ifconfig nomePlacaRede up
```

## Configurar data MÊS DIA HORA MINUTO ANO com

```css
date MMDDHHMMAAAA
```

## Formatar HD com parted para EFI

```css
parted -a optimal /dev/sda ↵  
(parted) mklabel gpt ↵  
(parted) rm 2 ↵  
(parted) unit mib ↵  
(parted) mkpart primary 1 513 ↵  
(parted) name 1 efi_grub ↵  
(parted) set 1 boot on ↵  
(parted) print ↵  
(parted) mkpart primary 514 1026 ↵  
(parted) name 2 boot ↵  
(parted) mkpart primary 1027 13315 ↵  
(parted) name 3 swap ↵  
(parted) mkpart primary 13316 -1 ↵  
(parted) name 4 rootfs ↵  
(parted) quit ↵  
```
## Formatando partições
```css
mkfs.vfat -F32 /dev/sda1  
```
```css
mkfs.ext2 /dev/sda2  
```

```css
mkfs.ext4 /dev/sda4  
```

```css
Swap  
```
```css
mkswap /dev/sda3  
```
```css
swapon /dev/sda3
```
## Criando e montando pastas para instação "não coloque a / no final"

```css
mkdir -p /mnt/gentoo  
```
```css
mount /dev/sda4 /mnt/gentoo  
```
```css
cd /mnt/gentoo  
```

## Verifique se a internet esta funcionado

```css
ping -c3 gentoo.org
```

## Baixando arquivo tar.bz2 para instalação, use o comando  
> links para navegar e achar o aquivo "stage3-amd64-*.tar.bz2"

```css
links https://www.gentoo.org/downloads/mirrors/#BR  

releases/ ↵  
amd64/ ↵  
autobuilds/ ↵  
current-stage3-amd64/ ↵  
stage3-amd64-*.tar.bz2
```

## Descompactar, use o nome completo

```css
tar xpf stage3-*.tar.bz2 --xattrs-include='*.*' --numeric-owner
```
```css
tar xpf stage3-amd64-*.tar.xz --xattrs-include='*.*' --numeric-owner
```

## Montando partições de boot/EFI

```css
mkdir -p /mnt/gentoo/boot  
```
```css
mount /dev/sda2 /mnt/gentoo/boot  
```
```css
mkdir -p /mnt/gentoo/boot/efi  
```
```css
mount /dev/sda1 /mnt/gentoo/boot/efi  
```

## Verifique/Edite o make.conf para o seu processador.

```css
grep -c processor /proc/cpuinfo número de núcleos  
```
```css
grep -m1 -A3 "vendor_id" /proc/cpuinfo modelo do processador [flags](https://wiki.gentoo.org/wiki/Safe_CFLAGS)  
```
```css
nano -w /mnt/gentoo/etc/portage/make.conf  
```

> Então adicione ao make.conf.
```css
CHOST="x86_64-pc-linux-gnu"  
COMMON_FLAGS="-march=native -O2 -pipe"  
CFLAGS="${COMMON_FLAGS}"  
CXXFLAGS="${COMMON_FLAGS}"  
#USE="acpi bindist git dbus ffmpeg jpeg jpeg2k -modemmanager mp3 mp4 mpeg png sound opengl X"  
EMERGE_DEFAULT_OPTS="--quiet-build=y --ask --load-average=2 --autounmask-write=y --with-bdeps=y"  
FEATURES="preserve-libs collision-protect candy"  
#INPUT_DEVICES="evdev keyboard synaptics mouse"  
#VIDEO_CARDS="vesa intel amdgpu nvidia virtualbox"  
LINGUAS="pt_BR.UTF-8 pt_BR.ISO-8859-1 pt_BR pt-BR"  
MAKEOPTS="-j4"  
ACCEPT_LICENSE="*"  
L10N="pt-BR"  
GRUB_PLATFORMS="efi-64"
```

> Observação: Editar make.conf para funcionar com UEFI. Adicione a linha:

```css
GRUB_PLATFORMS="efi-64"
```

## Montando pastas de instalação e configuração "não coloque / no final.

```css
mount --types proc /proc /mnt/gentoo/proc  
```
```css
mount --rbind /sys /mnt/gentoo/sys  
```
```css
mount --rbind /dev /mnt/gentoo/dev  
```
```css
test -L /dev/shm && rm /dev/shm && mkdir /dev/shm  
```
```css
mount --types tmpfs --options nosuid,nodev,noexec shm /dev/shm  
```
```css
chmod 1777 /dev/shm  
```

## Copie as informações de DNS

```css
cp -L /etc/resolv.conf /mnt/gentoo/etc/
```

## Entrando no novo ambiente

```css
chroot /mnt/gentoo /bin/bash  
```
```css
source /etc/profile && export PS1="(chroot) $PS1"
livecd ~ #
```
## Instalando um instantâneo do repositório ebuild da web

```css
emerge-webrsync  
```
```css
emerge --sync --quiet  
```

## Mirror select

```css
mkdir /etc/portage/repos.conf
```
```css
cp /usr/share/portage/config/repos.conf /etc/portage/repos.conf/gentoo.conf
```
```css
mirrorselect -i -o >> /etc/portage/repos.conf/gentoo.conf
```

## Escolhendo o perfil correto/de sua escolha

```css
eselect profile list
```
## Escolha o perfil com o comando... "substitua o X pelo número de sua escolha.
```css
eselect profile set "X"  
   default/linux/amd64/17.0/desktop
```

## Use eselect para verificar
```css
eselect profile list
```

## Atualize

```css
emerge --ask --update --deep --newuse @world
```
## Fuso horário.

```css
echo "America/Belem" > /etc/timezone  
```
```css
emerge --config sys-libs/timezone-data  
```

```css
nano -w /etc/locale.gen  
   pt_BR.UTF-8 UTF-8
```
```css
locale-gen  
```
```css
locale -a  
```
```css
eselect locale list  
```
```java
eselect locale set [pt_br.utf8]  
```
```css
nano -w /etc/env.d/02locale  
   LANG="pt_BR.UTF-8"  
   LC_COLLATE="C"
```

## Carregando o ambiente.
```css
env-update && source /etc/profile && export PS1="(chroot) $PS1"
```
```css
emerge -auDN @world
```

## CPU flags.

```css
emerge app-portage/cpuid2cpuflags  
```
```css
cpuid2cpuflags-x86  
```
```css
cpuid2cpuflags-x86 >> /etc/portage/make.conf  
```

> apague o primero CPU_FLAGS_X86 da linha do make.conf  
> modifique colocando entre aspas =""

```css
nano -w /etc/portage/make.conf  
```
```css
env-update && source /etc/profile && export PS1="(chroot) $PS1"  
```

## Instalando pacotes para o kernel.

> gentoo kernel

```css
emerge -aq gentoo-sources genkernel usbutils dosfstools pciutils gentoolkit ufed eix axel xfsprogs<
```
## Configurando o genkernel

```css
nano -w /etc/genkernel.conf
   MENUCONFIG="yes"  
   CLEAN="no"  
   MRPROPER="no"  

descomente:
   MAKEOPTS="$(portageq envvar MAKEOPTS)"  
   TMPDIR="/var/tmp/genkernel"
```
## Configurando o fstab.
```css
nano -w /etc/fstab
```

| fs | mountpoint | type | opts | dump/pass |
| :--- | :--- | :--- | :--- | :--- |
| /dev/sda1 | /boot/efi | vfat | noauto,noatime | 0 2 |
| /dev/sda2 |	/boot |	ext2 |	defaults |	0 2
| /dev/sda3 |	none |	swap |	sw |	0 0
| /dev/sda4 |	/ |	ext4 |	noatime |	0 1
| /dev/cdrom |	/mnt/cdrom |	auto |	noauto,ro |	0 0
| tmpfs |	/var/tmp |	tmpfs |	rw,nosuid,noatime,nodev,size=4G,mode=1777 |	0 0
| tmpfs |	/var/tmp/portage |	tmpfs |	rw,nosuid,noatime,nodev,size=4G,mode=775,uid=portage,gid=portage,x-mount.mkdir=775 |	0 0

## Verifique/Edite o make.conf para o seu processador.

```css
nano -w /etc/portage/make.conf
```
## Instalando o kernel.

```css
genkernel all
```

## Configurar e construir o kernel automaticamente.

```
--------------------------------------------------------------------------------------  
video card nvidea  
Pre-allocated buffer size for HD audio drive = 2048  
Virtualization drivers  
DOS/FAT/NT Filessystems ---> * NTFS write support  

Device Drivers --->  
  Generic Driver Options --->  
   [*] Maintain a devtmpfs filesystem to mount at /dev  
   [ ]    Automount devtmpfs at /dev, after the kernel mounted the rootfs  

Verify SCSI disk support has been activated (CONFIG_BLK_DEV_SD):  
KERNEL Enabling SCSI disk support  

Device Drivers --->  
  SCSI device support --->  
   <*> SCSI disk support  

File systems --->  
  <*> Second extended fs support  
  <*> The Extended 3 (ext3) filesystem  
  <*> The Extended 4 (ext4) filesystem  
  <*> Reiserfs support  
  <*> JFS filesystem support  
  <*> XFS filesystem support  
  <*> Btrfs filesystem support  
  DOS/FAT/NT Filesystems --->  
     <*> MSDOS fs support  
     <*> VFAT (Windows-95) fs support  

Pseudo Filesystems --->  
   [*] /proc file system support  
   [*] Tmpfs virtual memory file system support (former shm fs)  

Device Drivers --->  
  Network device support --->  
   <*> PPP (point-to-point protocol) support  
   <*>    PPP support for async serial ports  
   <*>    PPP support for sync tty ports  

Processor type and features --->  
  [*] Symmetric multi-processing support  

Device Drivers --->  
  HID support --->  
   -*- HID bus support  
   <*> Generic HID driver  
   [*] Battery level reporting for HID devices  
    USB HID support --->  
    <*> USB HID transport layer  
  [*] USB support --->  
   <*>   xHCI HCD (USB 3.0) support  
   <*>   EHCI HCD (USB 2.0) support  
   <*>   OHCI HCD (USB 1.1) support  

Architecture specific kernel configuration  

Processor type and features --->  
  [ ] Machine Check / overheating reporting  
  [ ]   Intel MCE Features  
  [ ]   AMD MCE Features  
  Processor family (AMD-Opteron/Athlon64) --->  
   ( ) Opteron/Athlon64/Hammer/K8  
   ( ) Intel P4 / older Netburst based Xeon  
   ( ) Core 2/newer Xeon  
   ( ) Intel Atom  
   ( ) Generic-x86-64  
Executable file formats / Emulations --->  
  [*] IA32 Emulation  

Enable GPT partition  

-*- Enable the block layer --->  
  Partition Types --->  
   [*] Advanced partition selection  
   [*] EFI GUID Partition support  

Enable EFI stub support and EFI variables in the Linux kernel if UEFI is used to boot the system (CONFIG_EFI, CONFIG_EFI_STUB, CONFIG_EFI_MIXED, and CONFIG_EFI_VARS):  
KERNEL Enable support for UEFI  

Processor type and features --->  
   [*] EFI runtime service support  
   [*]   EFI stub support  
   [*]     EFI mixed-mode support  

Firmware Drivers --->  
   EFI (Extensible Firmware Interface) Support --->  
     <*> EFI Variable Support via sysfs  
------------------------------------------------------------------------------------
```
## Configurando o OpenRC Logging

```css
nano -w /etc/rc.conf
   rc_parallel="YES"
```

## Se estiver instalando em um ssd

```java
nano -w /etc/profile.d/xdg_cache_home
   export XDG_CACHE_HOME="/tmp/${USER}/.cache"
```

## Instalando firmware

```java
emerge --ask sys-kernel/linux-firmware<
```

## Informação de host e domínio

```java
nano -w /etc/conf.d/hostname
   hostname="gentoo"
```   

## Configurando a rede  

>Primeiro instale o pacote net-misc/netifrc

```css
emerge --ask --noreplace net-misc/netifrc
```

> Iniciando automaticamente a rede durante o boot precisam ser criados assim como fizemos com o net.eth0.  
> Se após a inicialização do sistema descobrirmos que o nome que usamos para a interface de rede (que está atualmente documentada como eth0) está errado, então execute os seguintes passos para corrigir isso:  
> 1.Corrija o arquivo /etc/conf.d/net com o nome correto da interface de rede (tal como enp0s3 em vez de eth0).  
> 2.Crie um novo link simbólico (como /etc/init.d/net.enp0s3).  
> 3.Remova o link simbólico antigo (rm /etc/init.d/net.eth0).  
> 4.Adicione o novo ao runlevel default.  
> 5.Remova o antigo usando rc-update del net.eth0 default.

```css
cd /etc/init.d  
```
```css
ln -s net.lo net.enp0s3  
```
```css
rc-update add net.enp0s3 default  
```
```css
cd /
```

## O arquivo hosts

> Adicione um nome depois de localhost

```css
nano -w /etc/hosts  
   127.0.0.1 gentoo.homenetwork localhost
```

## Adicionando senha para o root

```css
passwd
```

## Teclado

```css
nano -w /etc/conf.d/keymaps  
   keymap="br-abnt2"
```


## Hora local.


```css
nano -w /etc/conf.d/hwclock  

   clock="local"
```


## Sistema de log


```css
emerge -aq sysklogd cronie mlocate logrotate dhcpcd  
```
```css
rc-update add sysklogd default  
```
```css
rc-update add sshd default  
```
```css
rc-update add cronie default  
```
```css
rc-update add dhcpcd default
```


## Selecionando/Instalando um gerenciador de boot: GRUB2.

> Obs: Cuidado no "grub.cfg" pois ao aperta tab ele altomaticamente coloca "grub.conf".


> Modo BIOS  

```css
emerge --ask sys-boot/grub  
```
```css
grub-install /dev/sda  
```
```css
grub-mkconfig -o /boot/grub/grub.cfg
```


## Verifique se a partição do EFI/boot esta criada/montada


> Modo UEFI  


```css
mkdir -p /boot/efi Verifique antes  
```

```css
mount /dev/sda1 /boot/efi Verifique antes  
```

```css
mount -o remount,rw /boot/efi  
```

```css
echo 'sys-boot/grub:2 mount truetype' >> /etc/portage/package.use/grub:2  
```

```css
emerge --ask --update --newuse --deep --verbose sys-boot/grub:2  
```

```css
mount -o remount,rw /sys/firmware/efi/efivars  
```

```css
grub-install --target=x86_64-efi --efi-directory=/boot/efi --removable  
```

```css
nano -w /etc/default/grub
   GRUB_TIMEOUT=1
```

```css
grub-mkconfig -o /boot/grub/grub.cfg  
```


## Adicionando um usuário e senha


```css
useradd -m -G users,wheel,audio,video,root,sys,disk,adm,bin,daemon,portage,console,usb,games,cron,input,lp,uucp -s /bin/bash nomeUsuario 
```
```css
passwd nomeUsuario
```


## Saindo do sistema



```css
exit  
```
```css
cd  
```
```css
umount -l /mnt/gentoo/dev{/shm,/pts,}  
```
```css
umount -R /mnt/gentoo  
```
```css
shutdown -r now  
```






