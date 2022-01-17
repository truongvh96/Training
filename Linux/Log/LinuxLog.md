#### 1. Log trong linux:
Mặc định các file log sẽ được ghi vào folder ``` /var/log - /var/log/syslog (Debian) & /var/log/message (RHEL) ``` lưu trữ toàn bộ hành vì trên hệ thống, bao gồm cả message khởi động. ``` - /var/log/auth.log (Debian) & /var/log/secure (RHEL) ``` lưu thông tin về sự kiện login trên hệ thống, hành động của user root, và các module plugin authentication (PAM) ``` - /var/log/kern.log ``` lưu kernel log, lỗi, warning, giúp troubleshoot các vấn đề với kernel. ``` - /var/log/cron ``` lưu thông tin về các cronjob trên hệ thống. ``` - /var/log/appname ``` lưu log của các ứng dụng như apache, mysql.

#### 2. Syslog là gì?:
Đây là 1 tiêu chuẩn để tạo và truyền log. Giao thức syslog sử dụng (RFC 514). Hoạt động ở tầng transport, truyền log qua network, dữ liệu được địch dnag theo các cấu trúc. Sử dụng port 514 (UDP) , 6514 (TCP) với message log được mã hóa.
Nó lắng nghe các sự kiến bằng việc tạo một socket located tại /dev/log, nơi ứng dụng có thể được ghi. Các log này có thể được forward tới một remote server (VD: ELK stack).
Một syslog message có định dạng header và content Syslog Format & Field:
Header: Phần header của syslog chứa một số trường: timestamp, hostname, application name, location & priority. VD :
Format của syslog có thể customize trên trong file cấu hình
#### 3. Logrotate
Công cụ giúp quản lý được việc sử dụng tài nguyên từ những file log
File cấu hình logrotate nằm ở đường dẫn /etc/logrotate.conf
```
# rotate log files weekly
weekly
```
Dòng trên thiết lập việc quay vòng log mặc định trên hệ thống
```
# keep 4 weeks worth of backlogs
rotate 4
```
Dòng trên định nghĩa các rotate log cũ được giữ bao lâu trước khi xóa khỏi hệ thống
```
# uncomment this if you want your log files compressed
#compress
```
Bỏ comment dòng trên nếu sử dụng nếu muốn nén file log
```
# packages drop log rotation information into this directory
```
```
include /etc/logrotate.d
```
Dòng trên trỏ các tệp tới folder /etc/logrotate.d nơi các pagkage khác có thể giữ log rotate file config
```
/var/log/apt/history.log {
rotate 12
monthly
compress
missingok
notifempty
}
```
Đoạn code trên dùng để quản lý rule rotate cho log của apt package, sử dụng với tất cả log của các ứng dụng khác trên hệ thống.
