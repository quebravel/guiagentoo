[Gentoo instalação base](README.md)

## Log como root, e instale o pacote essencial.
```
emerge sudo
```


```
vim /etc/sudoers
```
## Entre no arquivo sudoers e descomente a linha ou...

```
%wheel ALL=(ALL) ALL
nomeUsuário ALL=(ALL) ALL
```
> ou usar o comando su para instalar em modo root.

## Entre no arquivo issue e apage os digitos '.\0'
```
vim /etc/issue
```
## Gravando Horário [MÊS DIA HORA MINUTO ANO]
```
date MMDDHHMMAAAA
 ```
> grave no hardware com o comando..
```
hwclock --systohc
```

## Colocando uma bandeira no processador.

> Veja o número de processadores que sua maquina tem com o comando cat, depois entre em make.conf e acrescente a lina MAKEOPTS=-jX com o número de processadores some com mais +1\. Sendo X o número de processadores.
```
vim /etc/portage/make.conf
MAKEOPTS=-jX
```
## Atualizando.
```
emerge -uvDNa @world
```
## Instalando o X + CPUFlags.
```
emerge -aq x11-base/xorg-drivers
emerge -aq x11-base/xorg-x11
```
## Aplicativos úteis
```
emerge --ask --quiet x11-terms/xterm
app-editors/mousepad
x11-terms/xfce4-terminal
www-client/firefox
dev-vcs/git
net-misc/wget
```
## Controladores de som.
```
emerge --ask --quiet media-sound/pulseaudio
media-sound/alsa-utils
media-plugins/alsa-plugins## Setando e adicionando ao boot
```
```
/etc/init.d/dbus status
/etc/init.d/dbus start
rc-update add dbus default
```
```
/etc/init.d/consolekit status
/etc/init.d/consolekit start
rc-update add consolekit default
```

>(Usar quando usar um gerenciador de login)
```
rc-update add xdm default
/etc/init.d/xdm status
```
```
env-update && source /etc/profile
/etc/init.d/xdm start
```

>ou echo "exec bspwm" > ~/.xinitrc

```
/etc/init.d/alsasound status
/etc/init.d/alsasound start
rc-update add alsasound boot
alsamixe
```

#### Abra o terminal, digite alsamixer, dê play em algum som e tente aumentar a amplitude do som com as setas direcionais.
## Instalando internet.
> Veja se contem no USE no make.conf a palavra networkmanager,
> se não conter adicione-a
```
emerge --ask --quiet net-misc/networkmanager
```
## Iniciando a rede no boot.
> Adicione o widger.
> levante e adicione o genrenciador com:

```
rc-service NetworkManager status
```

```
rc-service NetworkManager start
```

```
rc-update add NetworkManager default
```
```
env-update && source /etc/profile
```

## Configurando teclado Xorg.

`Teclado`
```
nano /etc/X11/xorg.conf.d/30-keyboard.conf

Section "InputClass"
        Identifier "keyboard-all"
        Driver "evdev"
        Option "XkbLayout" "br"
        Option "XkbVariant" ",abnt2"
        Option "XkbOptions" "grp:shift_toggle,grp_led:scroll"
EndSection
```

## Removendo Tearing da placa de video.

`Intel`
```
nano /etc/X11/xorg.conf.d/10-intel.conf
Section "Device"
        Identifier "Intel Graphics"
        Driver "intel"
        Option "AccelMethod" "sna"
        Option "TearFree" "on"
        Option "DRI" "3"
EndSection
```



`Radeon`
```
nano /etc/X11/xorg.conf.d/20-device.conf
Section "Device"
        Identifier "Default"
        Driver "amdgpu"
        Option "DRI" "3"
        Option "TearFree" "True"
EndSection
```


## Configurando Touchpad.

```
nano -w /etc/X11/xorg.conf.d/70-synaptics.conf
Section "InputClass"
         Identifier "touchpad"
         Driver "synaptics"
         MatchIsTouchpad "on"
         Option "TapButton1" "1"
         Option "TapButton2" "3"
         Option "TapButton3" "2"
         Option "VertEdgeScroll" "on"
         Option "VertTwoFingerScroll" "on"
         Option "HorizEdgeScroll" "on"
         Option "HorizTwoFingerScroll" "on"
         Option "CircularScrolling" "on"
         Option "CircScrollTrigger" "2"
         Option "EmulateTwoFingerMinZ" "40"
         Option "EmulateTwoFingerMinW" "8"
         Option "CoastingSpeed" "0"
         Option "FingerLow" "30"
         Option "FingerHigh" "50"
         Option "MaxTapTime" "125"
EndSection
```


## Quando instalar o google chrome e ele fica pedindo
> senha para entrar no navegador, instale coloque uma senha e
> depois remova usando o aplicativo
### sudo emerge --ask app-crypt/seahorse
## Caso der erro de mask em uma instalação.

### Durante a instalação de um pacote, o portage reclama que o pacote só poder ser
> instalado ser for feita uma alteração de palavra-chave.
> the following keyword changes are necessary to proceed:

### required by *** argument
>use o comando:
### $ sudo emerge --ask pacote --autounmask-write
>em seguida
```
$ sudo etc-update --automode -5
```

```
$ sudo emerge --ask --quiet pacote
```
## Erro de depedência
### After a emerge --sync mjpegtools needed an update. Mjpegtools has unmet requirements

# emerge -u --deep --newuse world
>Calculating dependencies /

>!!! Problem resolving dependencies for media-video/mjpegtools from @selected
>... done!

>!!! The ebuild selected to satisfy "media-video/mjpegtools" has unmet requirements.
>- media-video/mjpegtools-2.0.0-r1 USE="X gtk png v4l -dga -dv -mmx -quicktime -sdl -sdlgfx -static-libs"

>The following REQUIRED_USE flag constraints are unsatisfied:
>X? (
>The above constraints are a subset of the following complete expression:
>!X? ( !gtk !sdl ) X? ( sdl ) sdlgfx? ( sdl )

>(dependency required by "@selected" [set])
>(dependency required by "@world" [argument])### Adicione a flag marcada em vemelho:
```
$ sudo USE=sdl emerge media-video/pacote
```
>Isso é temporário, para deixar em defenitivo adicione ao arquivo
```
$ sudo media-video/pacote
```
## Erro sartifazer depedência.
### emerge: there are no ebuilds built with USE flags to satisfy "dev-libs/openssl:0[bindist=]".
>!!! One of the following packages is required to complete your request:
>- dev-libs/openssl-1.0.2g-r2::gentoo (Change USE: -bindist)
>- dev-qt/qtnetwork-5.5.1::gentoo (Change USE: +bindist)
>(dependency required by "dev-qt/qtnetwork-5.5.1::gentoo" [ebuild])
>(dependency required by "dev-qt/qtwebkit-5.5.1-r1::gentoo" [installed])
>(dependency required by "@selected" [set])
>(dependency required by "@world" [argument])### Você precisa desabilitar o "bindist" use a flag no openssl para sartisfazer "dev-qt/qtnetwork" e "dev-qt/qtwebkit" em:
```
/etc/portage/make.conf
USE="-bindist"
```
# emerge -uD --changed-use --ask @world
## Erros de multiplas depedências
### $ sudo emerge -uvDNa @world

>These are the packages that would be merged, in order:

>Calculating dependencies... done!

>!!! Multiple package instances within a single package slot have been pulled
>!!! into the dependency graph, resulting in a slot conflict:

>dev-libs/libpcre:3

>(dev-libs/libpcre-8.41:3/3::gentoo, ebuild scheduled for merge) pulled in by
>(no parents that aren't satisfied by other packages in this slot)

>(dev-libs/libpcre-8.41:3/3::gentoo, installed) pulled in by
>=dev-libs/libpcre-8.38[<font color="red">pcre16,unicode] required by (dev-qt/qtcore-5.7.1-r3:5/5.7::gentoo, ebuild scheduled for merge)
## Desligar o computador sem sudo
### Para conceder permissão para que usuários comuns possam desligar o computador:
```
# chmod 4755 /sbin/shutdown
```
>Esse comando acima muda a permissão para desligar, em seguida crie um atalho:
```
# ln -s /sbin/shutdown /bin/
```
>Pronto! Agora você pode desligar e reiniciar o seu computador usando comandos sem precisar logar como root.
## Configuração de fontes.
### # emerge -aq media-libs/fontconfig-infinality

# eselect fontconfig list
```
Available fontconfig .conf files (* is enabled):
[1] 10-autohint.conf
[2] 10-hinting-full.conf
[3] 10-hinting-medium.conf
[4] 10-hinting-none.conf
[5] 10-hinting-slight.conf *
[6] 10-no-sub-pixel.conf
[7] 10-powerline-symbols.conf *
[8] 10-scale-bitmap-fonts.conf *
[9] 10-sub-pixel-bgr.conf
[10] 10-sub-pixel-rgb.conf
[11] 10-sub-pixel-vbgr.conf
[12] 10-sub-pixel-vrgb.conf
[13] 10-unhinted.conf
[14] 11-lcdfilter-default.conf *
[15] 11-lcdfilter-legacy.conf
[16] 11-lcdfilter-light.conf
[17] 20-unhint-small-dejavu-sans-mono.conf
[18] 20-unhint-small-dejavu-sans.conf
[19] 20-unhint-small-dejavu-serif.conf
[20] 20-unhint-small-vera.conf *
[21] 25-unhint-nonlatin.conf
[22] 30-metric-aliases.conf *
[23] 30-urw-aliases.conf *
[24] 40-nonlatin.conf *
[25] 42-luxi-mono.conf *
[26] 45-latin.conf *
[27] 49-sansserif.conf *
[28] 50-user.conf *
[29] 51-local.conf *
[30] 52-infinality.conf
[31] 57-dejavu-sans-mono.conf
[32] 57-dejavu-sans.conf
[33] 57-dejavu-serif.conf
[34] 60-latin.conf *
[35] 60-liberation.conf
[36] 62-croscore-arimo.conf
[37] 62-croscore-cousine.conf
[38] 62-croscore-symbolneu.conf
[39] 62-croscore-tinos.conf
[40] 65-fonts-persian.conf *
[41] 65-khmer.conf
[42] 65-nonlatin.conf *
[43] 69-unifont.conf *
[44] 70-no-bitmaps.conf
[45] 70-yes-bitmaps.conf
[46] 75-yes-terminus.conf *
[47] 80-delicious.conf *
[48] 90-synthetic.conf *
[49] 99pdftoopvp.conf
```

# eselect fontconfig enable X

# eselect infinality list
```
Available styles:
[1] debug
[2] infinality *
[3] linux
[4] nyx
[5] osx
[6] osx2
[7] win7
[8] win98
[9] winxp
```
# eselect infinality set X

# eselect lcdfilter list
```
Available styles:
[1] custom
[2] default *
[3] infinality
[4] infinality-classic
[5] infinality-nudge
[6] infinality-push
[7] infinality-sharpened
[8] infinality-shove
[9] linux
[10] nyx
[11] osx
[12] ubuntu
[13] vanilla
[14] windows-7
[15] windows-7-light
[16] windows-xp
[17] windows-xp-light
```
# eselect lcdfilter set X

## Arrumando fontes java.
### Adicione ao arquivo:
# vim /etc/environment
```
_JAVA_OPTIONS='-Dawt.useSystemAAFontSettings=<font color="red">lcd_hrgb'
```
## Detectar GPU para acrescentar em VIDEO_CARDS="".
### # lspci | grep -i VGA

>00:02.0 VGA compatible controller: Intel Corporation 3rd Gen Core processor Graphics Controller (rev 09)

>O resultado foi uma GPU de 3rd geração. Consulte a tabela no site do gentoo em intel e verifique qual é o drive adequado.
>Consultado o site do gentoo o indicado para o resultado acima será:

>Gen 3 915G/GM, 945G/GM, G/Q33, Q35, Atom D4xx/D5xx/N4xx/N5xx 1.4 No No No No <font color="red">intel i915
## Downgrade gentoo package

>Crie um arquivo com a versão escolhida em /etc/portage/package.mask(gentoo),
>/etc/portage/package.use(funtoo),
>o nome do arquivo terá o mesmo nome do pacote e o conteudo será o nome do pacote mais
>o número da versão escolhida.
