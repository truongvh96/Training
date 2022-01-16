1. DNS là viết tắt của cụm từ Domain Name System. Hệ thống phân giải tên miền. DNS thực hiện dịch tên miền thành địa chỉ IP để người dùng có thể dễ dàng truy cập website mà không cần nhớ IP. Hoặc nó cũng có thể phân giải ngược lại từ IP thành tên miền DNS sử dụng port 53 (UDP).

2. DNS hoạt động ntn?
Có 4 loại DNS Server liên quan đến việc hoạt động của DNS

- DNS recursor: đây là Server sẽ nhận các query từ người dùng (client) thông qua web browser. Sau đó DNS recursor gọi đến các Server khác (VD: Root DNS server) để lấy thông tin và trả về cho Client. Đồng thời nó cũng theo dõi DNS Record.
- Root Nameserver: Server này là bước đầu trong việc phân giải Domain thành địa chỉ IP. Nó giống như index trong thư việc để trỏ đến các giá sách cụ thể. Sau đó server này chuyển tiếp tới TLD nameserver để tìm kiếm theo các top-level cụ thể.
- TLD Nameserver (Top Level Domain): Server này là bước tiếp theo trong việc tìm kiếm địa chỉ IP. Nó lưu các phần cuối của hostname như ".com" ".vn". Server này trả về địa chỉ IP cho Recursor server.
- Authoritative nameserver: Server này thực giữ nắm giữ và chịu trách nhiệm các DNS record. Server này tra cứu và trả về bản ghi IP của hostname tương ứng với request tới DNS Recursor.
3. Các loại bản ghi DNS

SOA (Start of Authority) : Bản ghi chứa các thông tin về domain trên DNS server

Cú pháp:
```
[tên miền] IN SOA [tên-server-dns] [địa-chỉ-email] (serial number;refresh number;retry number;experi number;time-to-live number)
```
NS (Name Server): record này giúp định nghĩa các thông tin về máy chủ khai báo và quản lý tên miền,
Cú pháp:
```
[domain_name] IN NS [DNS-Server_name]
```
VD:
```
google.com IN NS ns.google.com
```
A: Bản ghi này rất quan trọng. Đây là bản ghi dùng để ánh xạ từ domain thành IP. và để trỏ tên miền tới Server Hosting website.
Cú pháp:
```
[domain_name] A [IP_ADDRESS]
```
VD:

AAAA bản ghi này tương tự A nhưng dùng cho IPv6.
CNAME cho phép tạo bí danh cho tên miền, một tên miền có thể có nhiều bí danh (alias) . Để sử dụng bản ghi này cần khai báo bản ghi A.
VD:
www   CNAME   goole.com
mail CNAME goole.com
example.com   A   103.101.161.201
PTR: Chuyển đổi ngược từ IPv4 hoặc IPv6 sang tên miền. VD:
90.163.101.103.in-addr.arpa       IN PTR     google.com.
SRV: Là bản ghi đặc biệt dùng để xác định chính xác dịch vụ nào chạy port nào, tại đây có thể thêm Priority, Name, Weight, Port, Points to, TTL.
VD:
```
ldap._tcp.example.com. 3600  IN  SRV  10  0  389  ldap01.example.com.
```
MX : MX record là một bản ghi chỉ định server nào quản lý các dịch vụ email của tên miền đó, có thể trỏ tên miền tới mail server, đặt mức độ ưu tiên (priority), đặt TTL.
Cú pháp:
```
[domain_name] MX [Priority_number] [subdomain_name]
```
VD:
```
example.com    MX    10    mail.example.com.
mail.example.com    A    103.101.161.201
```
TXT : Là record giúp bạn chứa các thông tin dạng text (văn bản) của tên miền, bạn có thể thêm Host mới, Giá trị TXT, TTL (Time to Live), Points to.
