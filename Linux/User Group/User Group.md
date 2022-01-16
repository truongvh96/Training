1. User:
File /etc/passwd bao gồm các thông tin: UID, GID, Home DIrectory , Default Shell File /etc/shadown lưu thông tin password của user được mã hóa. Hệ thống sẽ đọc file bất cứ khi nào người dùng đăng nhập.

1.1. Tạo mới user:
```
# sudo user add -m truong
```
Lệnh trên sẽ tạo ra user tên truong và -m là option tạo home directory cho user đó.
Các Option:
```
-d tạo home directory với đường dẫn tuyệt đối.
-u tạo user với UID cụ thể
-s chỉ định default shell cho user
```
1.2. Set or Update password cho user:
```
# sudo passwd add truong
```
1.3. Xóa user:
```
# userdel truong
```
1.4. Edit account:
```
# usermod -e 2021-12-31 truong
```
Các option:

-a thêm user mà không xóa ở nhóm khác
-e set ngày hết hạn của account
-l change user name
-L lock account
-U unlock account
-G add or remove mmembership in a group
-d change default directory
-s change default shell
-u change user UID
1.5. Yêu cầu password phức tạp cho user
```
# chage -m 5 -M 30 Max
```
lệnh trên sẽ buộc user khi update password không ít hơn 5 ngày và không quá 30 ngày cho lần update trước.
```
# chage -W 7 truong
```
Lênh trên thông báo cho user account password còn 7 ngày sẽ hết hạn
# chage --list truong
Lệnh trên list setting của user truong
B. Group
Group giúp tạo và quản lý các nhóm người dùng với các quyền cụ thể của từng group. File chứa thông tin của các group: /etc/group

2.1. Tạo group # groupadd
```
# sudo groupadd namegroup
```
2.2. Thêm người dùng vào group # usermod
```
# sudo usermod -a -G group user
```
VD:
```
# sudo usermode -a -G root truong
```
Lệnh trên sẽ thêm User truong vào group root
2.3. Chỉnh sửa thay đổi các group
```
groupmod [option] group
```
Các option: 
- -n đổi tên group 
- -g đổi group GID 
2.4. Xóa group groupdel
```
# sudo groupdel name
```
C. Phân biệt su & sudo, file /etc/sudoerc
su: dùng để chuyển user khác hoạc user có quyền root
sudo: dùng để chạy một command với quyền root
File /etc/sudoerc : File này control và cấu hình các user có thể chạy sudo command
Các tham số trong file cần lưu ý:
```
# root ALL=(ALL:ALL) ALL
```
root : định nghĩa user được apply
ALL (1): định nghĩa rule áp dụng cho toàn host
ALL (2): định nghĩa user root có thể run command với tất cả user khác
ALL (3): định nghĩa user root có thể run command với tất cả Group khác
ALL (4): Định nghĩa rule áp dụng cho tất cả command
%sudo	ALL=(ALL:ALL) ALL
Dòng trên định nghịa cho group sudo.
