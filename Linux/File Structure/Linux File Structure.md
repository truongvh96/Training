#### 1. /
Thư mục gốc của hệ thống tập tin

Mọi file đều bắt đầu tư thư mục này
Chỉ user roor có quyền ghi vào thư mục này
/root là thư mục home của user root, khác với thư mục /
#### 2. /bin
Cung cấp các command binarires cho toàn bộ user: cat, ls, cp, mv, ping

Chứa các tệp thực thi nhị phân
Chưa command chung cần để sử dụng single-user mode
Các command được sử dụng bởi toàn bộ user trên hệ thống
#### 3. /boot - các file khởi động
Chứa boot loader file, vd: kernel, initrd
#### 4. /dev - các file thiết bị
Bao gồm usb, devices, và bất kỳ thiết bị nào được cắm vào hệ thống.
VD: /dev/cdrom
#### 5. /etc - file cấu hình
Chứa file cấu hình cho tất cả các chương trình
Chứa script cho việc start/shutdown các chương trình.
VD /etc/resolv.conf, /etc/my.cnf
#### 6. /home 
thư mục home của người dùng chứa các file cá nhân của người dùng
#### 7. /lib - thư viện
Chứa các file cần thiết cho /bin/ và /sbin/
#### 8. /media
Các thiết bị gắn có thể được gỡ bỏ: CD-ROM, CD
#### 9. /mnt 
- temp cho việc mount filesystem
#### 10. /opt 
- option cho các phần mềm khác, add on
#### 11. /sbin 
- Các file binary cần thiết cho hệ thống
Các lệnh chạy với quyền administrator hoặc truy cập sâu vào hệ thống: iptables, reboot, fdisk, ifconfig.
#### 12. /srv 
- cung cấp dữ liệu cho các dịch vụ khác
#### 13. /tmp 
- các file tạm
#### 14. /usr 
- chương trình của người dùng
VD: /usr/local/apache2

#### 15. /proc 
- thông tin về các tiến trình trên hệ thống
