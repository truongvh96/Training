1.Tổng quan

- Open vSwitch là một soft-switch, một trong ba công nghệ cung cấp switch ảo trong hệ thống Linux (bên cạnh macvlan và Linux bridge), giải quyết vấn đề ảo hóa network bên trong các máy vật lý.
- Open vSwitch là phần mềm mã nguồn mở , giấy phép của Apache2.
- Openvswitch hỗ trợ nhiều công nghệ ảo hóa trên Linux bao gồm Xen/XenServer, KVM, and VirtualBox.
- Mã nguồn chủ yếu được viết bằng C, độc lập với các nền tảng và dễ dàng di chuyển giữa các môi trường.

2. OVS Architechture

- Các thành phần chính:
```
ovs-vswitchd đây là thành phần daemon thực hiện các chức năng chuyển mạch kết hợp Linux kernel module phục vụ cho flow-based switching.
ovsdb-server: lightweight database server mà ovs-vswitchd truy vấn tới để lấy cấu hình.
ovs-dpctl: tool dùng cấu hình switch kernel module.
ovs-vsctl: tiện ích để querying và updating cấu hình của ovs-vswitchd
ovs-appctl: tiện ích gửi commands để running Open vSwitch daemons. - Các tool được cung cấp thêm :
ovs-ofctl: tool dùng cho việc querying và controlling OpenFlow switches và controllers.
ovs-pki: tool dùng để tạo và quản lý public-key infrastructure cho OpenFlow switches.
ovs-testcontroller: cung cấp OpenFlow controller
```

3. Cài đặt openvswitch trên Ubuntu Server 16.04
```
apt-get install openvswitch-switch
```
4. Openvswitch Command
Lệnh ovs-vsctl để làm việc với Open vSwitch database. ovs-vsctl hiểu như là câu lệnh để querying tới database của OVS. Do đó, mọi sự thay đổi đều được lưu lại vào database (không mất khi khởi động lại OS)
```
Syntax:

ovs-vsctl [options] -- [options] command [args] [-- [options] command [args]]...
```
4.1 vSwitch
ovs-vsctl init: thiết lập ban đầu cho OVS gồm initial databse
ovs-vsctl show: Hiển thị ngắn gọn nội dung cấu hình có trong database.
ovs-vsctl show
Show thông tin của vswitch
ovs-vsctl add-br <bridge-name>
Tạo mội switch mới (mặc định switch này sẽ chưa có port nào )
ovs-vsctl add-br <fake-bridge> <parent-bridge> <vlan_id>
Tạo một fake bridge hỗ trợ vlan. parent-brige phải tồn tại trước khi tạo fake bridge. Fake bridge mới này sẽ hoạt động ở chuẩn Vlan 802.1Q.
Để kiểm tra vlan_id của fake bridge sử dụng lệnh: ovs-vsctl br-to-vlan <fake-bridge>
Hiển thị parent bridge của fake bridge sử dụng lệnh: ovs-vsctl br-to-parent <fake-bridge>
ovs-vsctl del-br <bridge>
Xóa một bridge và toàn bộ port trên bridge đó. Nếu là một bridge bình thường thì câu lệnh này đồng thời xóa hết cả fake bridge trên nó, và các port của nó.
ovs-vsctl br-to-vlan <bridge>
Hiển thị thông tin vlan_id nếu đó là fake bridge, nếu là real bridge thì không hiển thị 0 (không có vlan nào cả)
ovs-vsctl br-to-parent <bridge>
Hiển thị thông tin parent bridge của fake bridge, nếu đó là real bridge thì hiển thì chính nó.
4.2 Port
  ```
ovs-vsctl list-ports <bridge_name>
  ```
Liệt kê tất cả các port của bridge, thành từng dòng. Không liệt kê các local port.
  ```
ovs-vsctl add-port <bridge_name> <port_name>
  ```
Thêm 1 port vào switch
  ```
ovs-vsctl del-port <bridge-name> <port_name>
  ```
Xóa port
  ```
ovs-vsctl port-to-br <port_name>
  ```
Hiển thị thông tin tên cúa Bridge chứa port_name
  ```
ovs-ofctl dump-ports <switch_name>
  ```
List thông tin về các port numbers trên vswitch
  ```
ovs-ofctl show <switch_name>
  ```
List thông tin về port name và port number trên vswitch
  ```
ovs-vsctl add-port <brname> <ifname> -- set Interface <ifname> ofport=n
  ```
Add port và chỉ định port number trên switch (OVS version <= 1.9)
  ```
add-port <brname> <ifname> -- set Interface <ifname> ofport_request=n
  ```
Add port và chỉ định port number trên switch (OVS version > 1.9)
  ```
ovs-vsctl set interface <interface_name> type=<type_name>
  ```
Set kiểu cho port (interface, vxlan,gre,,,)
VD: Với port internal & Vlan, Vxlan & Gre
  ```
ovs-vsctl add-br ovstest 
ovs-vsctl add-port ovstest port1
ovs-vsctl set interface port1 type=internal
ovs-vsctl add-br ovstest1
ovs-vsctl add-port ovstest port1
ovs-vsctl add-br ovstest2
ovs-vsctl add-port ovstest port2
ovs-vsctl set interface port1 type=patch options:peer=port2
ovs-vsctl set Interface port2 type=patch options:peer=port1
ovs-vsctl add-port ovs1 vxlan1 -- set interface vxlan1 type=vxlan option:remote_ip=10.10.10.12
ovs-vsctl add-port ovs1 vxlan1 -- set interface vxlan1 type=vxlan option:remote_ip=10.10.10.11
  ```
  ```
ovs-vsctl set port <ifname> tag=<vlan-id>
  ```
Set Vlan cho port
  ```
ovs-vsctl add-port <brname> <ifname> tag=<vlan-id> -- set interface <ifname> type=<type_name>
  ```
Add port và set Vlan cho port:
4.3 STP
  ```
ovs-vsctl set Bridge <vswitch> stp_enable=<{true|flase}>
  ```
Turn { on | off } giao thức STP
  ```
ovs-vsctl set Bridge <vswitch> other_config:stp-priority=<prio>
  ```
Thiết lập bridge priority để chọn root bridge/switch
  ```
VD: ovs-vsctl set Bridge br0 other_config:stp-priority=0x7800
ovs-vsctl set Port <vswitch> other_config:stp-path-cost=<prio> Link tham khảo : http://manpages.ubuntu.com/manpages/xenial/man8/ovs-vsctl.8.html
  ```
5.Note quan trọng với Open vSwitch technology
5.1.Create vswitch
5.1.1.Create vswitch bằng command ovs-vsctl add-br
Tạo vswitch bằng command ovs-vsctl add-br thì vswitch là persistent vswitch.
Gán IP cho vswitch ip a add 10.10.10.5/24 dev truong
5.1.2.Create vswitch bằng file .xml
Với công nghệ Open vSwitch, ta chỉ có thể tạo network ở chế độ bridge, với vswitch đã tồn tại sẵn.
Có thể thêm vswitch bằng cách sửa file config của VM sử dụng virsh
5.2.Port
Khi vNIC hoặc pNIC được gắn vào vport on vswitch , vport sẽ sử dụng tên giống với interface NIC.
Nếu NIC chưa tồn tại sẽ có thông báo "could not open network device portlab (No such device)"
  ```
root@virsh02:~# ovs-vsctl show
255a18c6-eedc-4321-b497-2af2b95950ef
    Bridge truong
        Port truong
            Interface truong
                type: internal
        Port portlab
            Interface portlab
                error: "could not open network device portlab (No such device)"
        Port "ens33"
            Interface "ens33"
    ovs_version: "2.5.9"
  ```
Nếu không muốn hiện thông báo này cần set type cho port
ovs-vsctl set interface portlab type=internal
5.2.2.tap interface and uplink port
Port kết nối từ vNIC của VM vào vSwitch được gọi là tap interface.
Port kết nối vSwitch vào Physical gọi là Virtual Uplink Port
5.2.2.1.tap interface
Tap interface có MAC Address khác với vNIC trên VM
6.Network mode
6.1.Giới thiệu về chế độ bridge với Open vSwitch
Với libvirt API, Open vSwitch chỉ có chế bridge, không có 3 chế độ NAT, Routed, Isolated như đối với linux brdige.
1 bridge network chia sẻ 1 thiết bị Ethernet thực với virtual machines . Mỗi VM có thể có 1 địa chỉ IPv4 hoặc IPv6 sẵn có trên mạng LAN như 1 máy physical computer .
Bridging là tốt hơn về performace and đỡ rắc rối hơn các loại network trong libvirt.
Hạn chế:
Libvirt server buộc phải được kết nối LAN qua Ethernet .
6.2.Cấu hình bridged network trên Ubuntu server 16.04
a.Sử dụng command
Tạo vswitch và gán NIC
  ```
root@virsh02:~# ovs-vsctl add-br truong # Tạo vswitch mới tên truong
root@virsh02:~# ovs-vsctl add-port truong ens33 # Gán physical NIC vào vswitch
root@virsh02:~# ifconfig ens33 0 # Xóa IP cardc physical
root@virsh02:~# dhclient truong # Xin cấp cho vswitch truong dùng cho các kết nối vào ra qua switch này hoặc manual nếu không có dhcp
  ```
Kiểm tra
```
root@virsh02:~# ifconfig truong
truong    Link encap:Ethernet  HWaddr 00:0c:29:7c:4d:85  
          inet addr:192.168.18.21  Bcast:192.168.18.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe7c:4d85/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:6903 errors:0 dropped:5655 overruns:0 frame:0
          TX packets:46 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:701877 (701.8 KB)  TX bytes:5424 (5.4 KB)
```
```
root@virsh02:~# ifconfig truong
truong     Link encap:Ethernet  HWaddr 00:0c:29:7c:4d:85  
          inet addr:192.168.18.21  Bcast:192.168.18.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe7c:4d85/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:6903 errors:0 dropped:5655 overruns:0 frame:0
          TX packets:46 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:701877 (701.8 KB)  TX bytes:5424 (5.4 KB)
  ```
b.Cấu hình trong file /etc/network/interfaces
Tạo 1 bridge với 1 port physical
# The primary network interface
```
allow-truong ens33
iface ens33 inet manual
    ovs_bridge truong
    ovs_type OVSPort

# Bridge truong
auto truong
allow-ovs truong
iface truong inet dhcp
    ovs_type OVSBridge
    ovs_ports ens33
 ```
Restart Network và kiểm tra
```
root@virsh02:~# ifconfig truong
truong   Link encap:Ethernet  HWaddr 00:0c:29:7c:4d:85  
          inet addr:192.168.18.21  Bcast:192.168.18.255  Mask:255.255.255.0
          inet6 addr: fe80::20c:29ff:fe7c:4d85/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:416 errors:0 dropped:1 overruns:0 frame:0
          TX packets:27 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1 
          RX bytes:40538 (40.5 KB)  TX bytes:4554 (4.5 KB)
 ```
