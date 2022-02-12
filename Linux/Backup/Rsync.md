### 1. Rsync là gi ?
- Công cụ copy, backup trên hệ thống linux
- Rsync copy và giữ nguyên các thông số của file/folder như symbolic link, permission, timestamp, owner & group.
- Rsync sử dụng giao thức remote-update nên nhanh hơn scp, chỉ transfer những dữ liệu thay đổi.
- Rsync tiết kiệm băng thông do sử dụng phương pháp nén và giải nén khi transfer.
- Rsync không yêu cầu quyền super-user.
### 2. Cài đặt rsync
- Redhat/Centos
``` yum install rsync ```
- Ubuntu/Debian
``` sudo apt-get install rsync ```
### 3. Sytax & Option

Sytax: rsync options source destination Option:

``` -v ``` hiển thị trạng thái ra màn hình

``` -r: ``` copy dữ liệu recursively, nhưng không đảm bảo thông số của file và thư mục

``` -a: ``` cho phép copy dữ liệu recursively, đồng thời giữ nguyên được tất cả các thông số của thư mục và file

``` -z: ``` nén dữ liệu khi transfer, tiết kiệm băng thông tuy nhiên tốn thêm một chút thời gian

``` -h: ``` human-readable, output kết quả dễ đọc

``` --delete: ``` xóa dữ liệu ở destination nếu source không tồn tại dữ liệu đó.

``` --exclude: ``` loại trừ ra những dữ liệu không muốn truyền đi, nếu bạn cần loại ra nhiều file hoặc folder ở nhiều đường dẫn khác nhau thì mỗi cái bạn phải thêm --exclude tương ứng.

### 4. Ví dụ

- Copy file và thư mục trên local: Copy file trên local ``` rsync -zvh backup.tar /tmp/backups/ Copy ``` thư mục trên local ``` rsync -avzh /root/opt /tmp/backups/ ```
- Copy file và thư mục giữa các server: Copy thư mục từ Local lên Remote Server ``` rsync -avz rpmpkgs/ root@172.16.6.101:/home/ ``` Copy thư mục từ Remote Server về Local ``` rsync -avzh root@172.166.6.100:/home/truong/opt /tmp/opt ```
- Rsync qua SSH ``` rsync -avzhe ssh root@172.16.6.100:/root/install.log /tmp/ ```
- Hiển thị tiến trình trong khi transfer dữ liệu với ``` rsync rsync -avzhe ssh --progress /home/rpmpkgs root@162.16.6.100:/root/rpmpkgs ```
- Chạy thử nghiệm Rsync ``` rsync --dry-run --remove-source-files -zvh backup.tar /tmp/backups/ ```
- Tự động xóa dữ liệu nguồn sau khi đồng bộ thành công ``` rsync --remove-source-files -zvh backup.tar /tmp/backups/ ```
- Giới hạn dung lượng tối đa của file được đồng bộ ``` rsync -avzhe ssh --max-size='200k' /var/lib/rpm/ root@172.16.6.100:/root/tm ```
