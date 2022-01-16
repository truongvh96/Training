Starup Script Linux là Quá trình khởi động Linux sẽ có sự khác nhau giữa các Distro. Ở trong giai đoạn khởi động, user có thể đặt các script autostart hoặc program có thể là 1 command hoặc 1 chuỗi command hoặc một shell script (.sh) Các phiên bản Linux mới sẽ boot vào systemd, với các phiên bản cũ là System V Init. Cả 2 phương thức trên đều sẽ chạy cron và rc.local trước khi GNOME hoặc KDE được load.

1. Startup Program với Systemd
systemd là tiêu chuẩn cho trình quản lý hệ thống và service trong các hệ thống Linux mới . Nó chịu trách nhiệm thực thi và quản lý các program trong quá trình khởi động Linux.

- Kiếm tra các service
```
root@compute1:~# systemctl list-unit-files --type=service                                                                                                                                                          
UNIT FILE                                  STATE                                                                                                                                                                   
accounts-daemon.service                    enabled        
acpid.service                              disabled       
alsa-restore.service                       static
alsa-state.service                         static
alsa-utils.service                         masked         
anacron.service                            enabled        
apparmor.service                           enabled        
apport-autoreport.service                  static
apport-forward@.service                    static
apport.service                             generated
apt-daily-upgrade.service                  static
apt-daily.service                          static
autovt@.service                            enabled        
avahi-daemon.service                       enabled        
blk-availability.service                   enabled        
bluetooth.service                          enabled   
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
2. Tạo Starup service trong systemd
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
3. Sử dụng Cronjob
Để start program với cronjob có thể thêm dòng sau vào Crontab với trường @reboot
```
@reboot /opt/truong/startup.sh
```
4. Ngoài ra có thể sử dụng rc.local hoặc new bash session
Link tham khảo thêm : https://www.simplified.guide/linux/automatically-run-program-on-startup
