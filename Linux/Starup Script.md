- Starup Script Linux là Quá trình khởi động Linux sẽ có sự khác nhau giữa các Distro.
- Ở trong giai đoạn khởi động, user có thể đặt các script autostart hoặc program có thể là 1 command hoặc 1 chuỗi command hoặc một shell script (.sh) Các phiên bản Linux mới sẽ boot vào systemd, với các phiên bản cũ là System V Init.
- Cả 2 phương thức trên đều sẽ chạy **cron** và **rc.local** trước khi GNOME hoặc KDE được load.

 #### 1. Startup Program với Systemd
 
Systemd là tiêu chuẩn cho trình quản lý hệ thống và service trong các hệ thống Linux mới . Nó chịu trách nhiệm thực thi và quản lý các program trong quá trình khởi động Linux.

- Kiếm tra các service
```
root@compute1:~# systemctl list-unit-files --type=service                                                                                                                                                          
UNIT FILE                              STATE
accounts-daemon.service                enabled
acpid.service                          disabled
apache-htcacheclean.service            disabled
apache-htcacheclean@.service           disabled
apache2.service                        enabled
apache2@.service                       disabled
apparmor.service                       enabled
apport-autoreport.service              static
apport-forward@.service                static
apport.service                         generated
apt-daily-upgrade.service              static
apt-daily.service                      static
atd.service                            enabled
autovt@.service                        enabled
blk-availability.service               enabled
bootlogd.service                       masked
bootlogs.service                       masked
bootmisc.service                       masked
checkfs.service                        masked

```
Kiếm tra Service có được enable (Khởi động cùng hệ thống) không.
```
root@compute1:~# systemctl is-enabled isc-dhcp-server.service
enabled
```
Enable Service trong quá trình khởi động
```
# systemctl enable mysql
Synchronizing state of mysql.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable mysql
Created symlink /etc/systemd/system/multi-user.target.wants/mysql.service → /lib/systemd/system/mysql.service.
```
#### 2. Tạo Starup service trong systemd

Với các program không được quản lý bởi systemd, user có thể tạo script khởi động trong folfer /etc/systemd/system VD : Tạo 1 file /etc/systemd/system/truong-service.service với nội dung
```
[Unit]
Description=truong service

[Service]
ExecStart=/opt/truong/startup.sh start

[Install]
WantedBy=multi-user.target
```
/opt/startup.sh start đường dẫn tới program
Sau đó có thể enable program đó start cùng hệ thống
```
systemctl start truong-service
systemctl enable truong-service
```
#### 3. Sử dụng Cronjob

Để start program với cronjob có thể thêm dòng sau vào Crontab với trường @reboot
```
@reboot /opt/truong/startup.sh
```
#### 4. Ngoài ra có thể sử dụng rc.local

/etc/rc.local là file shell script được thực thi bởi systemd khi khởi động bằng cách dùng một dịch vụ có sẵn là rc-local.service. Nhờ đó, có thể thể sử dụng rc.local để thực thi câu lệnh hoặc bật dịch vụ cùng hệ thống.

Tạo file rc.local
Đầu tiên, bạn hãy tạo file /etc/rc.local như bên dưới:
```
$ sudo vim /etc/rc.local

## RHEL/CentOS/Fedora Linux edit the /etc/rc.d/rc.local

$ sudo vim /etc/rc.d/rc.local
```
Điền đoạn mã shell của bạn vào file để thực thi, trong bài viết này mình sẽ sử dụng đoạn script bên dưới:
```
#!/bin/sh
# add your commands 
# call your scripts here
 
# let us set stuff for my wifi
/sbin/iw phy0 wowlan enable magic-packet disconnect
 
# last line must be exit 0 
exit 0
```
Lưu lại thay đổi của file và gán quyền thực thi cho file:
```
$ sudo chmod -v +x /etc/rc.local
```
Kích hoạt rc-local.service
Để kích hoạt, sử dụng systemctl như bên dưới:
```
$ sudo systemctl enable rc-local.service
```
Sau đó, hãy khởi động lại hệ thống Linux của bạn và dùng systemctl để kiểm tra trạng thái:
```
$ sudo systemctl status rc-local.service
```
Nếu rc-local.service được chạy thì trạng thái sẽ báo như bên dưới:
```
● rc-local.service - /etc/rc.local Compatibility
     Loaded: loaded (/etc/systemd/system/rc-local.service; enabled-runtime; ven>
    Drop-In: /usr/lib/systemd/system/rc-local.service.d
             └─debian.conf
     Active: active (exited) since Wed 2020-11-04 13:29:54 IST; 1h 59min ago
       Docs: man:systemd-rc-local-generator(8)
      Tasks: 0 (limit: 37939)
     Memory: 0B
     CGroup: /system.slice/rc-local.service

Nov 04 13:29:54 nixcraft-wks01 systemd[1]: Starting /etc/rc.local Compatibility
Nov 04 13:29:54 nixcraft-wks01 systemd[1]: Started /etc/rc.local Compatibility.
```
### Hiển thị các cài đặt của dịch vụ

Để xem các cài đặt của dịch vụ, bạn có thể sử dụng systemctl như sau:
```
$ sudo systemctl cat rc-local.service
```
Lúc đó, các cài đặt của dịch vụ sẽ hiển thị:
```
# /etc/systemd/system/rc-local.service
#  SPDX-License-Identifier: LGPL-2.1+
#
#  This file is part of systemd.
#
#  systemd is free software; you can redistribute it and/or modify it
#  under the terms of the GNU Lesser General Public License as published by
#  the Free Software Foundation; either version 2.1 of the License, or
#  (at your option) any later version.
 
# This unit gets pulled automatically into multi-user.target by
# systemd-rc-local-generator if /etc/rc.local is executable.
[Unit]
Description=/etc/rc.local Compatibility
Documentation=man:systemd-rc-local-generator(8)
ConditionFileIsExecutable=/etc/rc.local
After=network.target
 
[Service]
Type=forking
ExecStart=/etc/rc.local start
TimeoutSec=0
RemainAfterExit=yes
GuessMainPID=no
 
# /usr/lib/systemd/system/rc-local.service.d/debian.conf
[Unit]
# not specified by LSB, but has been behaving that way in Debian under SysV
# init and upstart
After=network-online.target
 
# Often contains status messages which users expect to see on the console
# during boot
[Service]
StandardOutput=journal+console
StandardError=journal+console
```
