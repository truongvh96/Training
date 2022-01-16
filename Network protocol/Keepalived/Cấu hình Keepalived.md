1. Mô hình Lab Keepalived IP Failover
- Trong mô hình lab của bài này, ta sẽ có 2 Nginx Web Server (bạn có thể đổi thành HAProxy tùy ý) phục vụ xử lý request HTTP Web cơ bản. Hai Nginx WEB1 và WEB2 này sẽ được cấu hình dùng chung một VIP là 10.12.166.80. Bình thường thì VIP này sẽ do node Master phụ trách, node Backup sẽ ở trạng thái chờ.
- Khi có sự cố xảy ra với node Master như die server hay dịch vụ die thì node Backup sẽ nhận lấy VIP này và chịu trách nhiệm xử lý tiếp nội dung dịch vụ đang chạy cụ thể ở bài lab này là Nginx Web Server.
2. Cách thiết lập IP Failover với KeepAlived trên Ubuntu & Debian
cấu hình IP failover giữa các máy chủ Server1 và Server2.
Bước 1: Trước hết, Sử dụng lệnh sau để cài đặt các gói theo yêu cầu và cấu hình Keepaliving trên server:
```
$ sudo apt-get install linux-headers-$(uname -r)
```
Bước 2 - Cài đặt Keepalived (thực hiện ở cả 2 server)

Các gói Keepalived mặc định có sẵn trong kho apt. Vì vậy, bạn chỉ cần sử dụng một lệnh để cài đặt nó trên cả hai server:
```
$ sudo apt-get install keepalived
```
Bước 3 - Thiết lập Keepalived trên Server1.

Bây giờ bạn cần tạo hoặc chỉnh sửa tệp /etc/keepalived//keepalived/.conf trên Server1 và thêm các cài đặt phía dưới. Cập nhật tất cả các giá trị được tô màu tuỳ theo cấu hình mạng và hệ thống của bạn.
```
$ nano /etc/keepalived/keepalived.conf
! Configuration File for keepalived

global_defs {

notification_email {

sysadmin@mydomain.com

support@mydomain.com

}

notification_email_from Server1@mydomain.com

smtp_server localhost

smtp_connect_timeout 30

}

vrrp_instance VI_1 {

state MASTER

interface eth0

virtual_router_id 101

priority 101

advert_int 1

authentication {

auth_type PASS

auth_pass 1111

}

virtual_ipaddress {

192.168.10.121

}

}
```

Bước 4 - Thiết lập KeepAlived  trên Server2.

Bạn cũng cần tạo hoặc chỉnh sửa tệp cấu hình Keepalived: /etc/keepalivied/keepalived.conf trên Server2 và thêm vào cấu hình phần sau. Trong khi thực hiện các thay đổi cấu hình Server2, hãy đảm bảo các giá trị ưu tiên được đặt thấp hơn Server1. Ví dụ cấu hình bên dưới đang hiển thị giá trị ưu tiên là 100 so với Server1 là 101.
```
$ nano /etc/keepalived/keepalived.conf


! Configuration File for keepalived

global_defs {

notification_email {

sysadmin@mydomain.com

support@mydomain.com

}

notification_email_from Server2@mydomain.com

smtp_server localhost

smtp_connect_timeout 30

}

vrrp_instance VI_1 {

state MASTER

interface eth0

virtual_router_id 101

priority 100

advert_int 1

authentication {

auth_type PASS

auth_pass 1111

}

virtual_ipaddress {

192.168.10.121

}

}
```

Giá trị ưu tiên sẽ cao hơn trên master server, không quan trọng bạn sử dụng trạng thái gì. Nếu trạng thái của bạn là MASTER nhưng mức độ ưu tiên của bạn thấp hơn server BACKUP, bạn sẽ mất trạng thái MASTER.

Virtual_router_id phải giống nhau trên cả máy chủ Server1 và Server2.

Theo mặc định, vrrp_instance hỗ trợ tối đa 20 virtual_ipaddress. Để thêm nhiều địa chỉ, bạn cần thêm nhiều vrrp_instance



Bước 5 – Khởi động dịch vụ KeepAlived (thực hiện ở cả 2 server)

Khởi động KeepAlived bằng lệnh sau, lệnh cũng và cũng có thể sử dụng để cấu hình tự động chạy khi khởi động hệ thống.
```
$ sudo service keepalived start
```
Bước 6 - Kiểm tra IP ảo

Theo mặc định, IP ảo sẽ được gán cho master server. Trong trường hợp master bị ngắt, IP ảo sẽ được tự động gán sang backup server. Sử dụng lệnh sau để hiển thị IP ảo.
```
$ ip addr show eth0
```

Bước 7 - Kiểm tra lại thiết lập IP Failover

Tắt master server (Server1) và kiểm tra xem liệu IP có được gán tự động cho backup server (Server2) không.
```
$ ip addr show eth0
```
Bây giờ bạn khởi động Server1 và dừng Server2, IP sẽ được tự động chuyển về Server1.
```
$ ip addr show eth0
```
Xem các file log để đảm bảo dịch vụ vẫn chạy
```
$ tailf /var/log/syslog
```
