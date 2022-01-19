### Các bước cài đặt
#### 1) Cài đặt ban đầu trên cả 2 node
B1 : Update các gói phần mềm và các package cơ bản:
``` apt update -y ```

B2 : Thiết lập hostname :
``` nano /etc/hosts ```

B3 : Khai báo file /etc/hosts :
```
10.10.10.41 controller
10.10.10.40 compute
```
B4 : Thiết lập phân hoạch IP cho node controller
B5 : Disable firewalld và SELinux :
```
# systemctl disable ufw
# systemctl stop ufw
# reboot
```
### 2) Cài đặt OpenStack
#### 2.1) Cài đặt package OpenStack trên cả 3 node
Thực hiện các lệnh để cài đặt OpenStack :
```
# apt-get install -y software-properties-common
# add-apt-repository cloud-archive:train
# apt -y upgrade -y
# apt -y install crudini
# apt -y install python-openstackclient python3-openstackclient
```
#### 2.2) Cài đặt NTP
##### 2.2.1) Cài đặt NTP trên node controller
B1 : Cài đặt chrony sử dụng làm NTP :
```
# apt -y install chrony
```
B2 : Sao lưu file cấu hình của chrony :
```
# cp /etc/chrony/chrony.conf /etc/chrony/chrony.conf.bak
```
B3 : Set timezone :
```
# timedatectl set-timezone Asia/Ho_Chi_Minh
```
B4 : Sửa file cấu hình :
```
# server 0.vn.pool.ntp.org iburst
```
Node controller sẽ cập nhật thời gian từ internet hoặc máy chủ NTP. Các máy compute còn lại sẽ đồng bộ thời gian từ controller. Trong bài lab này sẽ sử dụng địa chỉ NTP của NIST là 128.138.140.44 (có thể thay thế bằng IP của NTP Server trong mạng).

B5 : Để cho phép các node compute kết nối với chrony trên node controller, ta sẽ sửa file cấu hình allow subnet chung của các node :
```
# allow 10.10.10.41/24
```
B6 : Khởi động lại chrony sau khi sửa file cấu hình :
```
# service chrony restart
```
B7 : Kiểm tra lại trạng thái của chrony :
```
# service chrony status
```
B8 : Kiểm tra xem thời gian đã được đồng bộ chưa :
```
# chronyc sources
```
Dấu "*" thể hiện việc đồng bộ thành công
```
210 Number of sources = 1
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^* time.cloudflare.com           3   8   377    81   -325us[ -323us] +/-   78ms
```
##### 2.2.2) Cài đặt NTP trên các node compute
B1 : Cài đặt chrony sử dụng làm NTP :
```
# apt -y install chrony
```
B2 : Sao lưu file cấu hình của chrony :
```
# cp /etc/chrony/chrony.conf /etc/chrony/chrony.conf.bak
```
B3 : Set timezone :
```
# timedatectl set-timezone Asia/Ho_Chi_Minh
```
B4 : Sửa file cấu hình :
```
# server 0.vn.pool.ntp.org iburst
# server controller iburst
```
2 node compute sẽ đồng bộ thời gian về từ controller

B6 : Khởi động lại chrony sau khi sửa file cấu hình :
```
# service chrony restart
```
B7 : Kiểm tra lại trạng thái của chrony :
```
# service chrony status
```
B8 : Kiểm tra xem thời gian đã được đồng bộ chưa :
```
# chronyc sources
```
Dấu "*" thể hiện việc đồng bộ thành công
```
210 Number of sources = 2
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^? realnames.com                 0   7     0     -     +0ns[   +0ns] +/-    0ns
^* controller                    4   6    17    49    -15us[  -98us] +/-   78ms
```

#### 2.3) Cài đặt và cấu hình memcached trên node controller
Cơ chế xác thực cho các dịch vụ sử dụng memcached để cache các token
Memcached thường chạy trên node controller
B1 : Cài đặt memcached :
```
# apt -y install memcached python-memcache
```
B2 : Backup file cấu hình của memcached :
```
# cp /etc/memcached.conf /etc/memcached.conf.bak
```
B3 : Cấu hình dịch vụ để sử dụng địa chỉ IP quản lý của node controller. Điều này là để cho phép truy cập từ các node khác thông qua dải VLAN MGNT (dải management). Chỉnh sửa file cấu hình memcached như sau :
```
# nano /etc/memcached.conf
```
Thay bang
```
-l 10.10.10.41
```
B4 : Khởi động lại memcached :
```
# service memcached restart
```
B5 : Kiểm tra trạng thái dịch vụ :
```
# service memcached status
```
#### 2.4) Cài đặt và cấu hình MariaDB trên node controller
Hầu hết các dịch vụ của OpenStack sử dụng cơ sở dữ liệu SQL để lưu thông tin. DB thường sẽ chạy trên node controller. Các dịch vụ OpenStack cũng hỗ trợ các cơ sở dữ liệu SQL khác bao gồm PostgreSQL.

B1 : Cài đặt MariaDB :
```
# apt -y install mariadb-server python-pymysql
```
B2 : Tạo và chỉnh sửa file cấu hình của OpenStack :
```
# nano /etc/mysql/mariadb.conf.d/openstack.cnf
```
Thêm vào đoạn sau:
```
[mysqld]
bind-address = 10.10.10.41

default-storage-engine = innodb
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
```
B3 : Khởi động MariaDB :
```
# service mysql restart
```
B4 : Kiểm tra trạng thái dịch vụ :
```
# service mysql restart
```
B5 : Bảo mật dịch vụ SQL bằng lệnh :
```
# mysql_secure_installation
```
Trong bước này, set password cho user root (VD : 'Welcome123)

#### 2.5) Cài đặt và cấu hình RabbitMQ trên node controller
- OpenStack sử dụng hàng đợi tin nhắn (Message queue) để phối hợp các hoạt động và thông tin trạng thái giữa các dịch vụ.

- Message queue thường chạy trên node controller. OpenStack hỗ trợ một số dịch vụ Message queue như là: RabbitMQ, Qpid, và ZeroMQ.

- Tuy nhiên, hầu hết các bản phân phối gói OpenStack đều hỗ trợ dịch vụ message queue cụ thể. Ta sẽ sử dụng RabbitMQ bởi vì hầu hết các phiên bản đều hỗ trợ nó.

B1 : Cài đặt rabbitmq :
```
# apt -y install rabbitmq-server
```
B2 : Khởi động dịch vụ rabbitmq :
```
# service rabbitmq-server start
```
B3 : Khai báo plugin cho rabbitmq :
```
# rabbitmq-plugins enable rabbitmq_management
```
B4 : Cấu hình trang quản lý rabbitmq trên UI :
```
# curl -O http://localhost:15672/cli/rabbitmqadmin
# chmod a+x rabbitmqadmin
# mv rabbitmqadmin /usr/sbin/
```
B5 : Tạo user openstack với mật khẩu tùy ý (VD : Welcome123)
```
# rabbitmqctl add_user openstack Welcome123
```
B6 : Gán quyền cho user vừa tạo :
```
# rabbitmqctl set_permissions openstack ".*" ".*" ".*"
# rabbitmqctl set_user_tags openstack administrator
```
Hiển thị danh sách user rabbitmq :
```
# rabbitmqadmin list users
```
B7 : Đăng nhập qua Web UI của RabbitMQ qua đường dẫn http://IP_MANAGER_CONTROLLER:15672 với user vừa tạo để kiểm tra :
```
http://172.16.6.71:15672
user: openstack
pass: Welcome123
```
#### 2.6) Cài đặt và cấu hình Etcd trên node controller
ETCD là một ứng dụng lưu trữ dữ liệu phân tán theo theo kiểu key-value, nó được các services trong OpenStack sử dụng lưu trữ cấu hình, theo dõi các trạng thái dịch vụ và các tình huống khác.
B1 : Cài đặt etcd :
```
# apt -y install etcd
```
B2 : Chỉnh sửa file cấu hình của etcd. Lưu ý thay đúng IP MGNT (10.10.230.10) và hostname controller đã được thiết lập trước đó .
```
# ETCD_NAME="controller"
# ETCD_DATA_DIR="/var/lib/etcd"
# ETCD_INITIAL_CLUSTER_STATE="new"
# ETCD_INITIAL_CLUSTER_TOKEN="controller"
# ETCD_INITIAL_CLUSTER="controller=http://10.10.10.41:2380"
# ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.10.10.41:2380"
# ETCD_ADVERTISE_CLIENT_URLS="http://10.10.10.41:2379"
# ETCD_LISTEN_PEER_URLS="http://0.0.0.0:2380"
# ETCD_LISTEN_CLIENT_URLS="http://10.10.10.41:2379"
```
B3 : Khởi động dịch vụ etcd :
```
# systemctl enable etcd
# systemctl start etcd
```
B4 : Kiểm tra trạng thái dịch vụ :
```
# systemctl status etcd
```
