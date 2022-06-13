Lưu ý:

– Phần cấu hình IP và routing bởi lệnh ip chỉ có tác dụng hiện hữu khi OS vận hành và sẽ mất toàn bộ thông tin cấu hình IP hoặc route khi OS Linux reboot.

### 1. Cấu hình quản lý địa chỉ IP

#### 1.1 Thêm địa chỉ IP tĩnh

Cú pháp :

**ip address add [ IFADDR ] dev [ NAME ]**

– Để cấu hình thêm 1 địa chỉ IP tĩnh trên máy chủ Linux ta sẽ cấu hình như sau. Nhớ thêm thông tin prefix subnet mask của địa chỉ IP.

**ip address add 192.168.1.5/24 dev eth0**

– Kiểm tra lại thông tin địa chỉ IP trên card mạng eth0.

**ip link show eth0**

``` eth0:  mtu 1500 qdisc pfifo_fast state UP group default qlen 1000

    link/ether 00:0c:29:3b:a4:e1 brd ff:ff:ff:ff:ff:ff

    inet 192.168.1.5/24 brd 192.168.1.255 scope global eth0

   	valid_lft forever preferred_lft forever

    inet6 fe80::20c:29ff:fe3b:a4e1/64 scope link

   	valid_lft forever preferred_lft forever
 ```

#### 1.2 Xoá địa chỉ IP tĩnh

Cú pháp :

**ip address del [ IFADDR ] dev [ NAME ]**

– Nếu bạn muốn xoá bỏ thông tin địa chỉ IP vừa tạo ra thì sẽ thực hiện cú pháp delete như sau.

 **ip address del 192.168.1.5/24 dev eth0**

#### 1.3 Xem thông tin IP

Cú pháp:

**ip address show [dev] [name]**

– Bạn muốn xem toàn bộ thông tin IP, MAC, Subnet trên hệ thống.

**ip add show**

```
1: lo:  mtu 65536 qdisc noqueue state UNKNOWN

    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

    inet 127.0.0.1/8 scope host lo

    inet6 ::1/128 scope host

   	valid_lft forever preferred_lft forever

2: eth0:  mtu 1500 qdisc pfifo_fast state UP qlen 1000

    link/ether 00:1c:42:ff:ff:ff brd ff:ff:ff:ff:ff:ff

    inet 192.168.1.5/24 brd 192.168.1.255 scope global eth0

    inet6 fe80::21c:42ff:feab:ffff/64 scope link

   	valid_lft forever preferred_lft forever
```
– Bạn muốn xem thông tin IP, MAC, Subnet của 1 card mạng cụ thể.

**ip addr show dev eth0**

```
2: eth0:  mtu 1500 qdisc pfifo_fast state UP qlen 1000

    link/ether 00:1c:42:ab:ac:ea brd ff:ff:ff:ff:ff:ff

    inet 103.199.8.27/24 brd 103.199.8.255 scope global eth0

    inet6 fe80::21c:42ff:feab:acea/64 scope link

   	valid_lft forever preferred_lft forever
```

### 2. Cấu hình thiết bị card mạng

#### 2.1 Up/down card mạng

Cú pháp :

**ip link set [ DEVICE ] { up | down}**

Ví dụ :

– Tắt chức năng hoạt động của card mạng.

**ip link set eth0 down**

– Bật chức năng hoạt động của card mạng.

**ip link set eth0 up**

#### 2.2 Show thông tin card mạng

Câu lệnh dùng để show thông tin về số lượng card mạng trên hệ thống, thông tin địa chỉ MAC, trạng thái up/down và vài thông tin cơ bản khác của các card mạng.

Cú pháp :

**ip link show [ DEVICE ]**

Ví dụ

**ip link show**
```
1: lo:  mtu 65536 qdisc noqueue state UNKNOWN

    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00

2: eth0:  mtu 1500 qdisc pfifo_fast state UP qlen 1000

    link/ether 00:1c:42:ab:ac:ea brd ff:ff:ff:ff:ff:ff
```

**ip link show eth0**
```

2: eth0:  mtu 1500 qdisc pfifo_fast state UP qlen 1000

    link/ether 00:1c:42:ab:ac:ea brd ff:ff:ff:ff:ff:ff
```

### 3. Cấu hình quản lý bảng định tuyến (routing table)
#### 3.1 Hiển thị thông tin bảng định tuyến

**ip route show**
```
default via 192.168.1.1 dev eth0

192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.5
```
#### 3.2 Thêm/xoá route tĩnh vào bảng định tuyến

Cú pháp:

**ip route { add | del } {NETWORK}**

– Muốn thêm thông tin route tĩnh vào bảng định tuyến của kernel, thì có thể thêm đối với network hoặc ip cụ thể.

**ip route add 192.168.1.0/24 via 192.168.1.1 dev eth0**

**ip route add 192.168.1.99 via 192.168.1.1 dev eth0**

– Xoá route tĩnh.

**ip route del 192.168.1.0/24 via 192.168.1.1 dev eth0**

**ip route del 192.168.1.99 via 192.168.1.1 dev eth0**

#### 3.3 Thêm/xoá default gateway route

Cú pháp:

**ip route [ add | del ] default via [ IP_GATEWAY ] dev [ NAME ]**

Ví dụ:

– Thêm thông tin default route gateway.

**ip route add default via 192.168.1.0 dev eth0**

– Xoá thông tin default route gateway.

**ip route del default via 192.168.1.0 dev eth0**

#### 3.4 Tìm route mà packet sẽ đi

– Option ‘route get‘ sẽ tìm thông tin route mà 1 địa chỉ IP sẽ được kernel sử dụng để định tuyến đường đi đến IP đích.

Cú pháp:

**ip route get {IP_ADDRESS}**

Ví dụ:

**ip route get 8.8.8.8**
```
8.8.8.8 via 192.168.1.1 dev eth0 src 192.168.1.5
```

### 3.5 Xoá bảng định tuyến

**ip route flush**

### 4. Cấu hình quản lý bảng ARP hệ thống

– Bạn muốn liệt kê thông tin bảng ARP mapping giữa địa chỉ IP và MAC Address của các máy tính cùng lớp mạng có giao tiếp qua lại với máy chủ Linux nội bộ thì bạn có thể thực hiện lệnh dưới.

**ip neighbour**
```

192.168.1.1 dev eth0 lladdr 88:a2:5e:bd:83:c0 DELAY

192.168.1.7 dev eth0 lladdr 34:40:b5:a0:92:80 STALE
```

– Thêm thông tin ARP entry vào bảng ARP. Ví dụ dưới sẽ add thông tin địa chỉ IP 192.168.1.99 mapping với giá trị địa chỉ MAC là 1:2:3:4:5:6 trên card mạng eth0.

**ip neigh add 192.168.1.99 lladdr 1:2:3:4:5:6 dev eth0**

– Xoá thông tin ARP entry trong bảng ARP

**ip neigh del 192.168.1.99 lladdr 1:2:3:4:5:6 dev eth0**

