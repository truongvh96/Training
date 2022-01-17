#### 1.
Ceph là giải pháp mã nguồn mở để xây dựng hạ tầng lưu trữ phân tán, ổn định, độ tin cậy và hiệu năng cao, dễ dàng mở rộng. Với hệ thống lưu trữ được điều khiển bằng phần mềm, Ceph cung cấp giải pháp lưu trữ theo đối tượng (Object), khối (Block) và tệp dữ liệu (File) trong một nền tảng đơn nhất. Ceph chạy trên nền tảng điện toán đám mây với các thiết bị phần cứng ổn định và tiên tiến nhất, giúp tiết kiệm chi phí và sử dụng dễ dàng với Linux Kernel.
2. Các hướng phát triển sử dụng Ceph
 

- Sử dụng thay thế lưu trữ trên ổ đĩa server thông thường.
- Sử dụng để backup, lưu trữ an toàn.
- Sử dụng để thực hiện triển khai các dịch vụ High Avaibility khác như Load Balancing for Web Server, DataBase Replication…
- Xây dựng Storage giải quyết bài toán phát triển lên Cloud Storage hoặc lưu trữ dịch vụ máy chủ  (Data as a Service).

 

3. Các hệ thống lưu trữ của Ceph
 

3.1. Ceph Object Storage (Hệ thống lưu trữ đối tượng của Ceph)
 

Ceph cung cấp khả năng truy cập liên tục tới các Object bằng cách sử dụng ngôn ngữ bản địa: binding hoặc radosgw, giao diện REST tương thích với các ứng dụng được viết cho S3 và Swift.

Thư viện phần mềm của Ceph cung cấp các ứng dụng cho khách hàng với khả năng truy cập trực tiếp tới hệ thống lưu trữ dựa trên RADOS Object và cung cấp một nền tảng cho một số tính năng cao cấp của Ceph, bao gồm RADOS Block Device (RBD), RADOS Gateway và Ceph File System.

 

3.2. Ceph Block Storage (Hệ thống lưu trữ khối dữ liệu của Ceph)
 

RADOS Block Device (RBD) cung cấp truy cập tới trạng thái "block device images", được đồng bộ hóa và sao chép trên toàn bộ storage cluster.

Hệ thống lưu trữ Object của Ceph không giới hạn native binding hoặc RESTful APIs. Người dùng có thể mount Ceph như một lớp cung ứng mỏng Block Device. Khi người dùng viết dữ liệu trên Ceph bằng cách sử dụng Block Device, Ceph tự động hóa đồng bộ và tạo bản sao dữ liệu trên Cluster, RADOS Block Device (RBD) của Ceph cũng tích hợp với Kernel Virtual Machine (KVM), mang lại việc lưu trữ ảo hóa không giới hạn tới KVM chạy trên Ceph client của người dùng.\
3.3. Ceph File System (Hệ thống lưu trữ file dữ liệu của Ceph)
 

Ceph cung cấp một POSIX-compliant network file system, nhằm mang lại hiệu suất cao, lưu trữ dữ liệu lớn và tương thích tối đa với các ứng dụng hiện tại.

Object storage system của Ceph cung cấp một số tính năng vượt trội hơn so với nhiều hệ thống lưu trữ Object hiện nay: Ceph cung cấp giao diện File System truyền thống với POSIX. Object storage system là một cải tiến đáng kể, nhưng chúng vẫn còn phải thực hiện nhiều hơn so với các File System truyền thống. Khi các yêu cầu về lưu trữ tăng lên cho các ứng dụng hiện tại, tổ chức cỏ thể cấu hình các ứng dụng hiện tại để sử dụng Ceph File System. Có nghĩa là người dùng có thể chạy một Storage Cluster cho Object, Block và lưu trữ dữ liệu dựa trên File.
4. Demo 
 

Server Client muốn kết nối tới Storage cần thực hiện:
- Cài client (ceph-client)
- Thực hiện mount hạ tầng lưu trữ ( fuse mount hoặc mount as a kernel driver).


Chi tiết:


Bước 1: Kiểm tra kernel


# lsb_release -a
# uname -r
Kiểm tra kernel có support không, check trong bảng 
http://ceph.com/docs/master/start/os-recommendations/


Bước 2: Trên server admin node, dùng ceph-deploy để cài đặt Ceph lên ceph-client node


# ceph-deploy install ceph-client
Ví dụ: # ceph-deploy install 192.168.1.119
Nhập password để có thể thực hiện cài trên client 192.168.1.119
Cài đặt báo ok và hiện version ceph cài thành công là ok.

 

Bước 3: Tạo mount point và tiến hành mount (fuse mount)

 

Trên server client tiến hành tạo thư mục để mount storage vào
$ sudo mkdir /home/{username}/cephfs
$ sudo ceph-fuse -m {ip-address-of-monitor}:6789 /home/{username}/cephfs
        Ví dụ:
       $ sudo mkdir /mnt/cephfs
       $ sudo ceph-fuse -m 192.168.1.114:6789, 192.168.1.113:6789, 192.168.1.117:6789 /mnt/cephfs


Bước 4: Kiểm tra mount thành công chưa


root@ceph-node1:/etc/ceph# df -h
Filesystem     Size      Used     Avail     Use%     Mounted on
/dev/md1       430G      2.8G    405G       1%           /
  udev             3.9G      4.0K     3.9G        1%           /dev
  tmpfs           798M     288K    798M        1%          /run
ceph-fuse        7.9T      60G      7.9T        7%           /mnt/cephfs
# mount
ceph-fuse on /mnt/cephfs type fuse.ceph-fuse (rw,nosuid,nodev,allow_other,default_permissions)

 

Bước 5: High Availability

 

• Monitor Node: monmap e3: 3 mons at {ceph-admin=192.168.1.117:6789/0,cephnode1=192.168.1.113:6789/0,ceph-node2=192.168.1.114:6789/0}, election epoch
150, quorum 0,1,2 ceph-admin,ceph-node1,ceph-node2
Client được mount với cả 3 monitor node, nên khi một monitor node faile sẽ không ảnh
hưởng đến kết nối từ client đến hạ tầng ceph.
• mdsmap e120: 1/1/1 up {0=ceph-admin=up:active}, 1 up:standby
Có thể tạo thêm các MDS chứa metadata một cách dễ dàng
• Fault tolerant: cơ chế tự sửa lỗi.
