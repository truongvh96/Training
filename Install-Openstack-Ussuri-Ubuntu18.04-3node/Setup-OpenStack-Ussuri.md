1. Cài đặt trên node controller

Lưu ý:
Đăng nhập với quyền root trên tất cả các bước cài đặt.
Các thao tác sửa file trong hướng dẫn này sử dụng lệnh Nano
Password thống nhất cho tất cả các dịch vụ là Welcome123

1.1 Cài đặt các thành phần chung

1.1.1 Thiết lập và cài đặt các gói cơ bản
Chạy lệnh để cập nhật các gói phần mềm

# apt-get -y update
Thiết lập địa chỉ IP

Cài đặt ifupdownn
# apt update && apt install -y ifupdown

Dùng lệnh nano để sửa file /etc/netplan/50-cloud-init.yaml với nội dung như sau.

network:
  ethernets:
    eth1:
      addresses:
        - 10.10.10.41/24
    eth0:
      addresses:
        - 172.16.6.71/24
      gateway4: 172.16.6.1
      nameservers:
        addresses: [ 172.16.6.0, 8.8.8.8 ]
  version: 2
  
Khởi động lại card mạng sau khi thiết lập IP tĩnh

# netplan generate
# netplan apply

Đăng nhập lại máy Controller với quyền root và thực hiện kiểm tra kết nối.

Kiểm tra kết nối tới gateway và internet sau khi thiết lập xong.

# ping google.com

Cấu hình hostname

Dùng vi sửa file /etc/hostname với tên là controller

 controller
Cập nhật file /etc/hosts để phân giải từ IP sang hostname và ngược lại, nội dung như sau

 cat <<EOT >> /etc/hosts                                                                               
> 10.10.10.41 controller                                                                                                    
> 10.10.10.40 compute                                                                                                     
> 10.10.10.39 block                                                                                                      
> EOT

2.1.2 Cài đặt NTP
Cài gói chrony

# apt-get -y install chrony
Mở file /etc/chrony/chrony.conf và tìm các dòng dưới

 Pool 0.debian.pool.ntp.org offline minpoll 8
 Pool 1.debian.pool.ntp.org offline minpoll 8
 Pool 2.debian.pool.ntp.org offline minpoll 8
 Pool 3.debian.pool.ntp.org offline minpoll 8
Thay bằng các dòng sau

 server 1.vn.pool.ntp.org iburst
 server 0.asia.pool.ntp.org iburst
 server 3.asia.pool.ntp.org iburst
Khởi động lại dịch vụ NTP

 service chrony restart
Kiểm tra lại hoạt động của NTP bằng lệnh dưới

 root@controller:~# chronyc sources
  
 ### Cấu hình tương tự 2 node còn lại
