# Ubuntu 16.04配置VNC进行远程桌面连接
+ 说明：其实要装VNC还不如装个Teamview来的快一些，并且全平台支持。
---
## 1.安装

```
$sudo apt-get install xfce4
$sudo apt-get install vnc4server
$sudo apt-get install xrdp
```

## 2.启动vncserver，初始化

```
$vncserver
#启动vncserver，第一次需要输入设置登录密码
```

`如果密码忘记了，可以进去~/.vnc/目录删除password文件即可。`

## 3.修改配置文件xstartup

```
$sudo gedit ~/.vnc/xstartup
```
在其中替换成如下的内容：
```
#!/bin/sh
# Uncomment the following two lines for normal desktop:
# unset SESSION_MANAGER
# exec /etc/X11/xinit/xinitrc
#[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
#[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
#xsetroot -solid grey
#vncconfig -iconic &
#x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
#x-window-manager &
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
vncconfig -iconic &
xfce4-session &
```

```
#!/bin/sh
# Uncomment the following two lines for normal desktop:
# unset SESSION_MANAGER
# exec /etc/X11/xinit/xinitrc
[ -x /etc/vnc/xstartup ] && exec /etc/vnc/xstartup
[ -r $HOME/.Xresources ] && xrdb $HOME/.Xresources
xsetroot -solid grey
vncconfig -iconic &
x-terminal-emulator -geometry 80x24+10+10 -ls -title "$VNCDESKTOP Desktop" &
#x-window-manager &
sesion-manager & xfdesktop & xfce4-panel &
xfce4-menu-plugin &
xfsettingsd &
xfconfd &
xfwm4 &
```
## 3.配置模拟终端,否则登录后无法启动终端
```
sudo update-alternatives --config x-terminal-emulator
```
`选择/usr/bin/xterm项`
## 4.重新启动vncserver与xrdp
```
sudo vncserver -kill :1
#杀死关闭vncserver

vncserver
#vncserver再次重启

sudo service xrdp restart
#重新启动xrdp

```

## 5.vncviewer
MobaxTerm 或 vncverwer
## 6.VNC中Tab键不能实现命令自动补全解决方法
```
cd ~/.config/xfce4/xfconf/xfce-perchannel-xml/
vim xfce4-keyboard-shortcuts.xml
```
找到：
```
<property name="<Super>Tab" type="string" value="switch_window_key"/>
```
```
<property name="<Super>Tab" type="empty"/>
```



Unbuntu 14.4安装之后就在终端中使用Tab键不能自动补全，解决办法如下：
1、利用vi编辑器打开 /etc/bash.bashrc文件（需要root权限）
sudo vi /etc/bash.bashrc

接下来会提示输入密码
2、找到文件中的下列代码

#enable bash completion in interactive shells
#if ! shopt -oq posix; then
#      if [-f  /usr/share/bash-completion/bash_completion ]; then
#          . /usr/share/bash-completion/bash_completion
#      elif [ -f /etc/bash_completion]; then
#           . /etc/bash_completion
#      fi
#fi

将注释符号#去掉，即改成
#enable bash completion in interactive shells
if ! shopt -oq posix; then
     if [-f  /usr/share/bash-completion/bash_completion ]; then
          . /usr/share/bash-completion/bash_completion
      elif [ -f /etc/bash_completion]; then
           . /etc/bash_completion
      fi
fi

3、最后 source一下 /etc/bash.bashrc即可， 即
```
sudo source /etc/bash.bashrc
```