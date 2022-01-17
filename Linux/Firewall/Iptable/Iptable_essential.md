#### 1. Netfilter
Linux Kernel có một package filtering framework là netfilter. Cho phép allow, drop, modify các traffic vào ra trên hệ thống.

#### 2. Iptable (command) là gì?
Iptables là giao diện dòng lệnh để tương tác với tính năng packet filtering của netfilter framework. Iptable được xây dựng dựa trên tính năng netfilter, cung cấp một firewall mạnh mẽ, với việc có thể thêm các rules lọc traffic vào ra trên hệ thống. Thêm vào đó nó còn có tính năng mở rộng fail2ban là một chương trình mạnh mẽ để block những traffic attackers.

Note: Iptable cung cấp 2 version khác sau với IPv4 và IPv6. Hai giao thức này có những sự khác biệt và được sử xử trong các kernel khác nhau. Do vậy command giữa IPv4 và IPv6 là khác nhau

#### 3. Iptable hoạt động thế nào?
Iptable cung cấp cơ chế lọc các gói tin với 3 thành phần khác nhau: tables, chain, targets. Table là thành phần cho phép xử lý các gói tin, table có 4 loại khác nhau, table mặc định trong Iptable là filter. Các Chain được gắn vào table. Chain cho phép kiểm tra traffic ở những điểm khác nhau. Khi một gói tin đến, Iptables sẽ đối chiếu với các rule trong chain. Khi khớp, nó đưa vào target để thực hiện liên kết chúng. Nếu không khớp với bất kỳ rule nào, nó sẽ thực hiện theo default policy của chain. Default policy cũng là 1 target, mặc định tất các các default policy này sẽ là cho phép gói tin đi qua.

##### 3.1. Tables
Có 4 tables phổ biến trên Linux:

- filter: là table mặc định và được sử dụng phổ biến. Được sử dụng để quyết định xem gói tin có được cho phép đến đích không.
- mangle: đây là table cho phép thay đổi phần header của gói tin (packet), VD: thay đổi giá trị TTL (Time to live)
- nat: table này cho phép định tuyến gói tin tới những host khác nhau trong mạng NAT, bằng việc thay đổi địa chỉ nguồn và đích của gói tin. Nó thường được sử dụng để cho phép truy cập các dịch vụ gián tiếp.
- raw: các gói tin trong iptable được kiểm tra theo trạng thái (state). raw table cho phép làm việc với các gói trước khi kernel theo dõi trạng thái của nó.
##### 3.2. Chains
Chain cho phép lọc gói tin ở nhiều điểm.

- PREROUTING: Rule áp dụng cho các gói đến Có trong tables nat, magle, raw.
- INPUT: Rule áp dụng với các gói trước khi tới local process. Có trong các tables magle và filter.
- OUTPUT: Rule áp dụng với các gói tin ra bên ngoài. Có trong tất các các tables.
- FORWARD: Áp dụng cho gói tin đến nhưng được chuyển tiếp sang nơi khác. Có trong các tables là mangle và filter.
- POSTROUTING: Rule này áp dụng cho gói khi đi ra Network Interface. Có trong các tables nat và mangle.
Biểu đồ luồng của gói qua chain

##### 3.3 Target
Các target bao gồm :

- ACCEPT: Cho phép gói tin đi qua
- DROP: Loại bỏ gói tin
- REJECT: Iptable sẽ từ chối gói tin, nó sẽ gửi các gói tin "connection rest" với TCP, “destination host unreachable” vớif UDP hoặc ICMP.
#### 4. Cấu hình &
##### 4.1. Block IP
VD:
```
iptables -t filter -A INPUT -s 172.16.6.250 -j REJECT
```
Lệnh trên sẽ block tất cả các gói tin từ IP 172.16.6.250 bằng cách thêm rule INPUT sử dụng filter table.
- ``` -t ``` chỉ định ra table sử dụng (vì filter là table mặc định nên có thể bỏ phần này iptables -A INPUT -s 192.168.254.250 -j REJECT
- ``` -A ``` nối thêm vào danh sách các rule INPUT (cho biết chiều lọc là ingoing)
- ``` -s ``` chỉ ra source IP
- ``` -j ``` chỉ định việc sử dụng target nào, cụ thể ở ví dụ trên là REJECT.
```
root@dell:~# iptables -L --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     udp  --  anywhere             anywhere             udp dpt:domain
2    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:domain
3    ACCEPT     udp  --  anywhere             anywhere             udp dpt:bootps
4    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:bootps
5    REJECT     all  --  192.168.254.250      anywhere             reject-with icmp-port-unreachable
```
Block với 1 dải mạng:
```
iptables -A INPUT -s 172.16.6.0/24 -j REJECT
```
Block gói tin ra ngoài :
```
iptables -A OUPUT -s 172.16.6.110 -j REJECT
```
##### 4.2 List rule
```
root@dell:~# iptables -L --line-numbers
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     udp  --  anywhere             anywhere             udp dpt:domain
2    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:domain
3    ACCEPT     udp  --  anywhere             anywhere             udp dpt:bootps
4    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:bootps

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  anywhere             192.168.122.0/24     ctstate RELATED,ESTABLISHED
2    ACCEPT     all  --  192.168.122.0/24     anywhere            
3    ACCEPT     all  --  anywhere             anywhere            
4    REJECT     all  --  anywhere             anywhere             reject-with icmp-port-unreachable
5    REJECT     all  --  anywhere             anywhere             reject-with icmp-port-unreachable

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     udp  --  anywhere             anywhere             udp dpt:bootpc
```
##### 4.3. Delete Rules
```
iptables -D INPUT -s 172.0/24 -j REJECT
```
Có thể xóa theo số dòng :
```
iptables -D INPUT 2
```
Xoá theo toàn bộ chain :
```
iptables -F INPUT
```
##### 4.4. Insert, replace rule
Insert Rule
```
iptables -I INPUT 1 -s 172.16.6.1 -j ACCEPT
```
- ``` -I ``` sử dụng để insert
``` 1 ``` & ``` 6 ``` chỉ ra số dòng insert Replace Rule
```
iptables -R INPUT 6 -s 172.16.6.4 -j ACCEPT
```
``` R ``` sử dụng để replace
```
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  172.16.6.1        anywhere            
2    ACCEPT     udp  --  anywhere             anywhere             udp dpt:domain
3    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:domain
4    ACCEPT     udp  --  anywhere             anywhere             udp dpt:bootps
5    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:bootps
6    ACCEPT     all  --  172.16.6.4        anywhere
```
##### 4.5. Sử dụng với các giao thức & Module
Sử dụng các giao thức để lọc Traffic. Block TCP incoming traffic (có thể sử dụng với UDP và ICMP, với IPv6 là ipv6-icmp)
```
iptables -A INPUT -p tcp -j DROP
```
- ``` -p: ``` chỉ ra giao thức sử dụng
Block SSH từ một dải IP
```
iptables -A INPUT -p tcp -m tcp --dport 22 -s 172.16.6.0/24 -j DROP
```
- ``` --dport: ``` chỉ ra port sử dụng
- ``` -m: ``` sử dụng module
Block SSH và Telnet từ dải IP
```
iptables -A INPUT -p tcp -m multiport --dports 22,23 -s 59.45.175.0/24 -j DROP
```
``` multiport: ``` module cho phép nhiều port trên 1 rule.
##### 4.6. Các state của gói tin

- NEW: Trạng thái của gói đầu tiên của một kết nối.
- ESTABLISHED: Trạng thái được sử dụng cho các gói đã được kết nối và nhận được phản hồi từ host.
- RELATED: Trạng thái này được sử dụng cho các kết nối có liên quan đến một kết nối ESTABLISHED khác.
- INVALID: Trạng thái này có nghĩa là gói không có trạng thái thích hợp.
- UNTRACKED: Trạng thái của gói không được theo dõi kết nối
- DNAT: Là trạng thái ảo sử dụng đại diện cho các gói có destination đã bị thay đổi bởi các rule trong bảng nat.
- SNAT: Giống DNAT nhưng đó đại diện cho các gói đã có source thay đổi.
VD: Loại bỏ các gói tin với trạng thái INVALID
```
iptables -A INPUT -m conntrack --ctstate INVALID -j DROP
```
##### 4.7. Thay đổi default policy

Policy mặc định có 3 rule:
``
Chain INPUT (policy ACCEPT)
...
Chain FORWARD (policy ACCEPT)
...
Chain OUTPUT (policy ACCEPT)
..
```
Để thay đổi policy sử dụng lệnh
```
iptables -P INPUT DROP
```
Lệnh trên chuyển policy của INPUT sang DROP.

##### 4.8. Sử dụng NIC

Ví dụ : Trên server có nhiều NIC, các NIC kết nối tới các dịch vụ khác nhau, khi đó sẽ cần viết rule theo interface.
```
iptables -A INPUT -i ens32 -j ACCEPT
```
- ``` -i ``` sẽ chỉ định input Interface. Lệnh trên sẽ accept các gói tin đi NIC ens32
iptables -A OUTPUT -o ens35 -d 121.18.238.0/29 -j DROP
- ``` -o ``` dùng cho output interface. Lệnh trên chặn đầu ra trên card ens35 tới network 121.18.238.0

##### 4.9. Điều kiện đặc biệt
```
iptables -A INPUT -p tcp -m multiport ! --dports 22,80,443 -j DROP
```
! dùng để phủ nhận các điều kiện còn lại
Lệnh trên sẽ cho phép tất cả traffic TCP cho port 22, 80, 443 và loại bỏ tất cả những cái khác.

##### 4.10. Block các gói tin TCP không hợp lệ sử dụng module tcp

iptables -A INPUT -p tcp -m tcp --tcp-flags ALL FIN,PSH,URG -j DROP

##### 4.11. Ghi log

Các log trên iptable được ghi vào nơi lưu trữ log hệ thống như ``` /var/log/syslog hoặc /var/log/messages ```
```
iptables -A INPUT -p tcp -m tcp --tcp-flags FIN,SYN FIN,SYN -j LOG
```
Lệnh trên sẽ ghi lại log của các gói tin incoming của giao thức TCP với các flag.
```
iptables -A INPUT -p tcp -m tcp --tcp-flags FIN,SYN FIN,SYN -j LOG --log-prefix=iptables:
```
``` --log-prefix ``` tuỳ chọn này cung cấp để dễ dàng tìm kiếm log sau khi xuất.

##### 4.12. Backup & Restore rule

Backup rules:
```
iptables-save > iptables.rules
```
Restore rules:
```
iptables-restore < iptables.rules
```
##### 4.13. Save

Các cấu hình sẽ được lưu trên RAM, do vậy nếu khởi động lại hệ thống sẽ mất các rules hiện tại để lưu cấu hình
```
sudo /sbin/iptables-save
```
