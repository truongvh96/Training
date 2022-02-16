### 1. RAID là gì?
Redundant Array of Inexpensive Disks: Là một kỹ thuật ảo hóa cho phép gom nhiều đĩa cứng vật lý thành một nhóm ổ logic, nhằm mục đích gia tăng tốc độ truy xuất hoặc giảm nguy cơ hỏng mất dữ liệu. Raid gồm 1 nhòm từ 2 ổ cứng trở lên được cấu hình và liên kết với nhau qua 1 Raid controller có thể là phần cứng hoặc mềm. Có 2 công nghệ phổ biến là Raid cứng và Raid mềm.

### 2. Các loại Raid phổ biến:
- Raid 0: Loại raid này cần tối thiểu 2 ổ đĩa cứng. Các block được ghi theo tỉ lệ 50:50 trên 2 ổ, loại raid này có thể giúp tăng hiệu năng IOP Raid 0 không có tính bảo mật về dữ liệu.
- Raid 1: Loại raid này cần tối thiêu 2 ổ, dữ liệu được ghi giống nhau trên 2 ổ. nếu 1 ổ bị hỏng dưx liệu vẫn hoạt động bình thường.
- Raid 5: Yêu cầu ít nhất 3 ổ cứng, loại raid này sẽ dùng ít nhất 1 ổ để khi thông số parity phục vụ cho việc restore data nếu có sự cố trên cụm raid.
- Raid 6: Tương tự raid 5 nhưng có chế lưu và tính parity phức tạp hơn raid 5, Yêu cầu tối thiểu 4 ổ do có 2 ổ cho việc lưu block backup.
- Raid 10: là kết hợp của Raid 1 và Raid 0.
### 3. Lệnh mdadm thao tác Software Raid Linux
Mdadm là một CLI để quán lý software raid trên linux.

Show help: ``` # mdadm --manage --help ```
Tạo Raid 1 cho nhóm 2 đĩa:
```
# mdadm --create --verbose /dev/md0 --level=1 /dev/sda1 /dev/sdb2
```
or
```
# mdadm -Cv /dev/md0 -l1 -n2 /dev/sd[ab]1
```
File cấu hình raid trên Centos /etc/mdadm.conf vs Ubuntu /etc/mdadm/mdadm.conf
Sau khi tạo raid cần khai báo vào file cấu hình:
```
# mdadm --detail --scan >> /etc/mdadm/mdadm.conf
```
Xoá disk khỏi raid:
```
# mdadm /dev/md0 --fail /dev/sda1 --remove /dev/sda1
```
Add disk vào cụm raid có sẵn:
```
# mdadm --add /dev/md0 /dev/sdb1
```
Check status group raid:
```
# cat /proc/mdstat
or 
# mdadm --detail /dev/md0
```
Thông số hiển thị - U là raid đang hoạt động, -F khi có 1 disk failed.
Stop & Delete Raid:
```
# mdadm --stop /dev/md0` & `mdadm --remove /dev/md0
```
### Tăng dung lượng partition không cần tắt máy https://manhpt.com/2020/12/12/tang-dung-luong-phan-vung-o-cung-tren-linux/
