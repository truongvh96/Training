1. NAT là một kỹ thuật cho phép một hoặc nhiều địa chỉ IP nội miền chuyển đổi sang một hoặc nhiều địa chỉ IP ngoại miền
2. Phân loại NAT

Hiện nay có 3 loại NAT phổ biến mà bạn cần biết đó là: Static NAT, Dynamic NAT và NAT Overload. Cụ thể các loại kỹ thuật NAT này như thế nào, hãy cùng tìm hiểu ngay sau đây:

- Static NAT là kỹ thuật dùng để thay đổi, biến một IP này thành một IP khác. Bằng cách sử dụng phương pháp cố định cụ thể từ địa chỉ IP cục bộ sang Public. Toàn bộ quá trình này được thực hiện và cài đặt thủ công.

Phương pháp Static NAT sẽ đặc biệt phát huy hiệu quả nếu các thiết bị có địa chỉ cố định để truy cập internet từ bên ngoài.

Cách cấu hình Static NAT như sau:

Thiết lập mối quan hệ chuyển đổi giữa địa chỉ IP cục bộ và Public bên ngoài:
```
Router (config) # ip nat inside source static [local ip] [global ip]
```
Xác định cổng kết nối với mạng nội bộ:
```
Router (config-if) # ip nat inside
```
Xác định cổng kết nối với mạng bên ngoài:
```
Router (config-if) # ip nat outside
```
- Dynamic NAT là kỹ thuật dùng để ánh xạ một địa chỉ IP này sang một địa chỉ IP khác (one – to – one) bằng phương pháp tự động. Thông thường, Dynamic NAT sẽ chuyển đổi từ IP mạng cục bộ sang địa chỉ IP được đăng ký hợp lệ.

Cách cấu hình Dynamic NAT như sau:

Xác định địa chỉ IP của mạng bên ngoài:
```
Router (config) # ip nat pool [name start ip] [name end ip] netmask [netmask]/prefix-lenght [prefix-lenght]
```
Thiết lập ACL để tạo danh sách các địa chỉ mạng cục bộ được phép chuyển đổi IP:
```
Router (config) # access-list [access-list-number-permit] source [source-wildcard]
```
Thiết lập mối quan hệ giữa địa chỉ nguồn (được thiết lập trong ACL) và địa chỉ IP hợp lệ bên ngoài:
```
Router (config) # ip nat inside source list <acl-number> pool <name>
```
Xác định cổng kết nối với mạng nội bộ:
```
Router (config-if) # ip nat inside
```
Xác định cổng kết nối với mạng bên ngoài:
```
Router (config-if) # ip nat outside
```
- NAT Overload còn có tên gọi khác là PAT (Port Address Translation). Đây là một dạng biến thể khác của Dynamic NAT. Nó cũng thực hiện chuyển đổi địa chỉ IP một cách tự động. Tuy nhiên, kiểu chuyển dịch địa chỉ của NAT Overload là dạng many – to – one (ánh xạ nhiều địa chỉ IP thành 1 địa chỉ IP) và dùng các chỉ số cổng (port) khác nhau để phân biệt cho từng chuyển đổi.

Cách cấu hình NAT Overload như sau:

Xác định các địa chỉ IP mạng nội bộ cần ánh xạ ra bên ngoài:
```
Router (config) # access-list <ACL-number> permit <source> <wildcard>
```
Cấu hình để chuyển địa chỉ IP đến cổng kết nối với mạng bên ngoài:
```
Router (config) # ip nat inside source list <ACL-number> interface <interface> overload
```
Xác định các cổng kết nối với mạng bên trong:
```
Router (config-if) # ip nat inside
```
Xác định các cổng kết nối với mạng bên ngoài:
```
Router (config-if) # ip nat outside
```
