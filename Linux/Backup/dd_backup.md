Lệnh dd dùng để đọc và ghi phục vụ cho backup các phân vùng hoặc ổ cứng : Cú pháp : # dd if="src" of="dest"

Các option:

- Backup toàn ổ cứng ``` # dd if = /dev/sda of = /dev/sdb ```
- Backup phân vùng ra image ``` # dd if=/dev/hda1 of=~/partition.img ```
- Backup phân vùng ra phân vùng khác ``` # dd if=/dev/sda2 of=/dev/sdb2 ```
- Tạo image cho ổ cứng ``` # dd if = /dev/sda of = ~/sdadisk.img ```
- Restore ổ từ image ``` # dd if = hdadisk.img of = /dev/sdb ```
- Tạo file ISO từ cdrom ``` # dd if = /dev/cdrom of = tgsservice.iso bs = 2048 ```
- Tạo phân vùng swap ``` # dd if=/dev/zero of=/root/swap bs=1024M count=1 ```

Các option :

- bs=Bytes Quá trình đọc (ghi) bao nhiêu byte một lần đọc (ghi)
- cbs=Bytes Chuyển đổi bao nhiêu byte một lần
- count=Blocks thực hiện bao nhiêu Block trong quá trình thực thi câu lệnh
- if Chỉ đường dẫn đọc đầu vào
- of Chỉ đường dẫn ghi đầu ra
- ibs=bytes Chỉ ra số byte một lần đọc
- obs=bytes Chỉ ra số byte một lần ghi
- skip=blocks Bỏ qua bao nhiêu block đầu vào
- conv=Convs Chỉ ra tác vụ cụ thể của câu lệnh, các tùy chọn được ghi bên dưới.

Option của conv:

- ascii Chuyển đôi từ mã EBCDIC sáng ASCII
- ebcdic Chuyển đổi từ mã ASCII sang EBCDIC
- lcase Chuyển đổi từ chữ thường lên hết thành chữ in hoa
- ucase Chuyển đổi từ chữ in hoa sang chữ thường
- nocreat Không tạo ra file đầu ra
- noerror Tiếp tục sao chép dữ liệu khi đầu vào bị lỗi
- sync Đồng bộ dữ liệu với ổ đang sao chép sang
