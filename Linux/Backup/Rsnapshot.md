Rsnapshoot là một tiện ích opensoure cho phép snapshot filesystem tại các thời điểm khác nhau có thể thực hiện trên local hoặc remote với các hệ thống Filesystem của Linux/Unix. Rnapshoot sử dụng hardlink, tạo ra một bản fullbackup, nó chiếm dung lượng cho một bản fullbackup và thay đổi sau đó của Filesystem . Sử dụng với SSH có thể thực hiện snapshoot với filesystem trên các máy chủ Linux/Unix từ xa.

1. Cài đặt rnapshot
Trên RHEL/CentOS:

Cài đặt gói epel:
```
yum install epel-release
```
Cài đặt rsnapshoot:
```
yum install rsnapshot
```
Trên Debian/Ubuntu:
```
apt-get update
apt-get install rsnapshot
```

2. Cấu hình
File cấu hình mặc định: ``` /etc/rsnapshot.conf ```

File cấu hình mẫu: ``` /etc/rsnapshot.conf.default ```

Một số Option quan trọng trong file config:

- ``` snapshot_root: ``` chỉ định thư mục lưu trữ toàn bộ backup VD: snapshot_root /data/backup
- ``` cmd_cp: ``` chỉ ra đường dẫn tới GNU cp program trên hệ thống.
- ``` cmd_rsync: ``` đường đẫn cần thiết tới rsync để có thể sử dụng rsnapshot.
- ``` cmd_ssh: ``` enable ssh để sử dụng snapshot tới các remote server, cần sử dụng ssh key không có passpharases.
- ``` cmd_logger: ``` đường dẫn đến log program cho việc sử dụng syslog.
- ``` cmd_du: ``` đường dẫn tới path của du program. rnapshot sẽ sử dụng "du" để tạo report về dung lượng ổ đĩa sử dụng
- ``` exclude_file: ``` có thể loại trữ các file không muốn snapshot.
- ``` interval: ``` set tham số tương ứng để gọi lệnh snapshot và số bản snapshot sẽ được giữ định kỳ.
```
interval        hourly  6
interval        daily   7
interval        weekly  4
interval        monthly 3
```
- ``` ssh_arg: ``` thông số về port ssh nếu có thay đổi.
- ``` backup: ``` lưu thông tin về filesystem nguồn (đường dẫn tuyệt đố) và folder backup đích (đường dãn tương đối tới thư mục bên trong folder snapshot_root).
VD: backup /etc/ localhost/
- ``` backup_script: ``` Tham số về đường dẫn backup script muốn thực thi và đường dẫn local muốn lưu trữ. 
VD:
```
backup_script      /usr/local/bin/backup_pgsql.sh       localhost/postgres/
```
3. Test config
```
rsnapshot configtes
```
Nếu trạng thái trả về OK thì file config đã đúng.
```
rsnapshot hourly
```
Lệnh trên sẽ thực hiện snapshot thư mục /etc/ với đích là folder snapshot_root
```
root@ansible_lab:/data/backup# ll
total 32
drwxr-xr-x 8 root root 4096 Thg 1 15 10:25 ./
drwxr-xr-x 3 root root 4096 Thg 1 13 09:11 ../
drwxr-xr-x 3 root root 4096 Thg 1 15 10:25 hourly.0/
drwxr-xr-x 3 root root 4096 Thg 1 15 10:24 hourly.1/
drwxr-xr-x 3 root root 4096 Thg 1 15 10:24 hourly.2/
drwxr-xr-x 3 root root 4096 Thg 1 15 10:24 hourly.3/
drwxr-xr-x 3 root root 4096 Thg 1 15 10:24 hourly.4/
drwxr-xr-x 3 root root 4096 Thg 1 15 10:24 hourly.5/
```
Các bản snapshot được lưu từ từ hourly.0 -hourly.6 sau khi bị xoá và quay vòng lại tương ứng với tham số interval hourly 6 trong file cấu hình
