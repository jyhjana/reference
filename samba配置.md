# Ubuntu16.04下搭建samba
## 1.安装samba
```
+ 安装samba：sudo apt-get install samba
+ 安装smbclient：sudo apt-get install smbclient
```

## 2.修改配置文件
### 如果是想设置一个无需验证的任何人都可以访问的共享文件夹
+ `sudo vim /etc/samba/smb.conf` 编辑smb.conf 文件,在配置文件的最末尾加上：
```
[share] #不能和文件夹同名
comment=Shared Folder require password    ##这个是说明，随意填
path=/home/share
public=yes
create mask=0775
directory mask=0775

##要能修改文件夹，要么就将/var/www/html权限设置为777，或者加上如下设置
forceuser=root
forcegroup=root
```

### 如果是想设置一个需要账号登陆才能访问的共享文件夹

+ 你需要一个系统用户（新建或者已存在）,例如新建一个系统用户share:
```
kc@kcp3-desktop:~$sudo adduser share
```
建好后，将此用户添加到samba用户
```
kc@kcp3-desktop:~$sudo smbpasswd -a share
kc@kcp3-desktop:~$sudo smbpasswd -e share
```

+ 配置smb.conf文件,`sudo vim /etc/samba/smb.conf` 编辑smb.conf 文件,在配置文件的最末尾加上：

```
[share]
comment = Shared Folder require password
path = /home/share
public = yes
writable = yes
valid users = share
create mask = 0777
directory mask = 0777
force user = nobody
force group = nogroup
available = yes
browseable = yes
```

+ 保存后重新启动samba：sudo /etc/init.d/samba restart

## 3.设置用户和密码
+ 添加用户：sudo useradd share ((前提是无此用户)
+ sudo smbpasswd -a share 接着提示输入密码。
  (前提是添加了此用户：useradd share，
   否则系统在没有此用户的前提下按默认操作是会报如下错的：
   Failed to add entry for user
```
kc@tyc-virtual-machine:/home#  smbpasswd -a share
New SMB password:
Retype new SMB password:
Failed to add entry for user share.
```
解决办法:只需增加相应的系统账号share就可以了:
```
kc@tyc-virtual-machine:/home#  groupadd share -g 6000
[kc@tyc-virtual-machine:/home# useradd share -u 6000 -g 6000 -s /sbin/nologin -d /dev/null
```
# 实现linux与windows之间
 win7 右键添加一个网络位置，输入 文件夹框 \\ip地址\share,按提示输入用户名密码即可。


# 实现linux与liunx之间
```
sudo ount -t cifs //192.168.13.41/share ~/yanhogn.jia/datasets -o username=share,password=Kc123456,sec=ntlmssp
```
# 附加
为了增加系统的安全性，该文件夹只想给share用户有读写权限即可!
```
[root@ubuntu ~]# chown -R share /home/share/
```
若不想让另人访问，只让share用户可以访问，只需执行命令:
```
[root@ubuntu ~]# chmod u+rwx,g+rwx,o-rwx /home/share
```
# NOTE
在linux内设置share文件夹权限为0777 （chmod 0777 share）
才可将windows的文件放到share文件夹里面