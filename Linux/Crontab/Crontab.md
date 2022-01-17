#### 1. Cron là gì?
Là một cách để tạo và chạy các lệnh theo một chu kỳ xác định. Đây là tiện ích giúp lập lịch trình để chạy những dòng lệnh bên phía server nhằm thực thi một hoặc nhiều công việc nào đó theo thời gian được lập sẵn.
#### 2. Crontab làm việc như thế nào?
Một cron schedule đơn giản là một text file. Mỗi người dùng có một cron schedule riêng, file này thường nằm ở /var/spool/cron . Crontab files không cho phép bạn tạo hoặc chỉnh sửa trực tiếp với bất kỳ trình text editor nào, trừ phi bạn dùng lệnh crontab.

Một số lệnh thường dùng:

``` crontab -e: ``` tạo hoặc chỉnh sửa file crontab 

``` crontab -l: ``` hiển thị file crontab 

``` crontab -r: ``` xóa file crontab
#### 3. Cách cài đặt Crontab
Sử dụng lệnh:
```
yum install cronie
```
Start crontab và tự động chạy mỗi khi reboot:
```
service crond start 
chkconfig crond on
```
## Linh hướng dẫn sử dụng
https://vietnix.vn/crontab/
