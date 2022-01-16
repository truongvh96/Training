1. Phân quyền (Permission)
Các quyền có trên linux:

``` r ``` Read với File.

``` w ``` Write với file. Với thư mục, file có thể create hoặc remove.

``` x ``` Execute với file. Với thư mục file có thể được list.
Các đối tượng đi theo:
```
u User
g Group
o Other
```
Octal:
```
1 Execute
2 Write
3 Write + Execute
4 Read
5 Read + Execute
6 Read + Write
7 Read + Write + Cú pháp: # chmod +rwx <filename> 
```
VD:
```
# chmod o+w filename Thêm quyền ghi cho tất cả user
# chmod g-r filename Xóa quyền đọc với group
```
```
chmod 664 myfile.txt Phân quyền sử dụng octal
6: Cho biết user có quyền Read + Write.
6: Group có quyền Read +
4: Other (User khác) có quyền Read
```

Thứ tự set quyền cho file & folder là: Owner -- Group -- Other

2. Owership & Group
``` chown user:group filename: ``` Thay đổi owner của 1 file hoặc folder

``` chgrp group_name filename: ``` Chỉ thay đổi group của 1 file hoặc folder.

``` -R ``` : đệ quy với trường hợp có nhiều subfolder bên trong.
