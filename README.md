# EeePC701InstallxWindow

承接上一視頻, 我已介紹過將Eee PC 701安裝最新版本的Alpine Linux並透過腳本方式進行與AI對話.
但這就是Eee PC 701還能做的唯一玩意嗎? 當然不是, 今次我便教大家再發掘一下它的各種玩法.

首先我們先解決Linux CLI環境下的不友善, 我們為它裝使用者圖形介面, 請執行以下指令:
```
apk add xorg-server xinit xf86-input-libinput xf86-video-intel alsa-utils xfce4 xfce4-terminal lightdm lightdm-gtk-greeter font-wqy-zenhei musl-locales firefox
```
再設定一下鍵盤及觸控板, 請執行:
```
mkdir -p /etc/X11/xorg.conf.d

cat > /etc/X11/xorg.conf.d/10-input.conf <<EOF
Section "InputClass"
     Identifier "libinput pointer catchall"
     MatchIsPointer "on"
     Driver "libinput"
EndSection
Section "InputClass"
     Identifier "libinput keyboard catchall"
     MatchIsKeyboard "on"
     Driver "libinput"
EndSection
EOF

cat > /etc/X11/xorg.conf.d/30-touchpad.conf <<EOF
Section "InputClass"
     Identifier "libinput touchpad"
     MatchIsTouchpad "on"
     MatchDevicePath "/dev/input/event*"
     Driver "libinput"
     Option "Tapping" "on"
     Option "TappingButtonMap" "lrm"
EndSection
EOF
```
執行以下指令會自動安裝運行圖形環境所需的組件:
```
setup-xorg-base
```
編輯.profile文件, 將系統語言設為繁體中文:
```
nano ~/.profile
```
為檔案加入以下內容:
```
export LANG="zh_TW.UTF-8"
export LC_ALL="zh_TW.UTF-8"
```
再執行以下指令便可進入圖形介面了:
```
startx
```
到此Alpine的應用與Windows上的操作已分別不大了, 以下的操作供大家參考, 有需要可跟著去做.

工作列顯示電池餘量(安裝後到電源設定中啟用):
```
apk add xfce4-power-manager dbus dbus-x11
rc-update add dbus
```
工作列顯示WiFi連線狀態與管理功能:
```
apk add networkmanager networkmanager-cli networkmanager-wifi network-manager-applet
rc-service networkmanager start
nmcli device set wlan0 managed yes
```
自動掛載USB盤:
```
apk add gvfs gvfs-fuse udisks2
setup-devd udev
```
掛載Samba服務需使用的套件:
```
apk add gvfs-smb avahi
rc-service avahi-daemon start
```
安裝查看及編輯圖片工具:
```
apk add feh gimp
```
安裝影音播放工具:
```
apk add mpv
```
編輯配置檔:
```
nano ~/.config/mpv/mpv.conf
```
檔案中加入以下內容:
```
vo=x11
```
安裝辦公軟件:
```
apk add libreoffice
```
安裝ssh服務:
```
apk add openssh-server
```
安裝被遠端連接桌面的工具:
```
apk add xrdp xorgxrdp
```
設定配置:
```
nano /etc/xrdp/startwm.sh
```
檔案最尾添加:
```
startxfce4
```
為用戶創建配置:
```
echo "xfce4-session" > ~/.xsession
echo "xfce4-session" > /root/.xsession
```
啟動服務:
```
rc-service xrdp start
rc-service xrdp-sesman start
```
安裝遠端控制RDP服務的客戶端工具:
```
apk add remmina
```


