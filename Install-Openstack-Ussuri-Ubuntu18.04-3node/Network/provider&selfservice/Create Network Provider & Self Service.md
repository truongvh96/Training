Create provider network
1. Acces Admin account
```
$ . admin-openrc
```
2. Create the network
```
$ openstack network create  --share --external \
  --provider-physical-network provider \
  --provider-network-type flat provider
```
--share option để cho phép tất cả project sử dụng virtual network.

--external định nghĩa virtual network là mạng external (nếu sử dụng --internal là vùng mạng internal.

-provider-physical-network provider và --provider-network-type flat option dùng kết nối viirtual dưới dạng flat (không sử dụng vlan/tag) ứng với interface được cấu hình provider trong neutron config linux brige agent.
3. Tạo subnet
```
openstack subnet create --network provider \
  --allocation-pool start=10.3.53.200,end=10.3.53.210 \
  --dns-nameserver 8.8.4.4 --gateway 10.3.52.1 \
  --subnet-range 10.3.53.0/22 provider
```
--network provider tên network.
--allocation-pool start=10.3.53.200,end=10.3.53.210 tương ứng dhcp pool.
--dns-nameserver 8.8.4.4 --gateway 10.3.52.1 dns và gateway cho network.
--subnet-range 10.3.53.0/22 provider subnet.
Create Self Service Network
Tạo mạng Self-Service với user demo Cần tạo provider network trước khi tạo self service network

1. Gain access
```
$ . demo-openrc
```
2. Create the network
```
$ openstack network create selfservice
```
3. Tạo subnet
```
openstack subnet create --network selfservice \
  --dns-nameserver 8.8.4.4 --gateway 172.16.1.1 \
  --subnet-range 172.16.1.0/24 selfservice
```
4. Tạo Router Self Service kết nối đến provider cần sử dụng virtual router để thực hiện NAT mỗi router có 1 interface cho self service và 1 gateway trên provider network đê định tuyến.
```
$ openstack router create router
```
5. Add self service network subnet vào interface của router
```
openstack router add subnet router selfservice
```
6. Set gateway provider vào router
```
$ openstack router set router --external-gateway provider
```
Kiểm tra trên controller node
1. Source admin
```
$ . admin-openrc
```
2. List network namespace
```
root@controller:~# ip netns
```
qrouter-95bc08f8-a37a-47ea-9b06-d2c230486384 (id: 2)
qdhcp-5e87b8c7-d233-4aaa-9fb3-b9817ff54df1 (id: 1)
qdhcp-727276b4-2f8b-49f1-a230-b518231d2aac (id: 0)
3. Show ip network namespace
```
root@controller:~# ip netns exec qrouter-95bc08f8-a37a-47ea-9b06-d2c230486384 ip a
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: qr-9940e6c7-13@if13: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    link/ether fa:16:3e:76:71:66 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.16.1.1/24 brd 172.16.1.255 scope global qr-9940e6c7-13
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:fe76:7166/64 scope link 
       valid_lft forever preferred_lft forever
3: qg-b033381a-55@if14: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether fa:16:3e:ff:da:f6 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.3.53.205/22 brd 10.3.55.255 scope global qg-b033381a-55
       valid_lft forever preferred_lft forever
    inet 10.3.53.203/32 brd 10.3.53.203 scope global qg-b033381a-55
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:feff:daf6/64 scope link 
       valid_lft forever preferred_lft forever
```
root@controller:~# ip netns exec qdhcp-5e87b8c7-d233-4aaa-9fb3-b9817ff54df1 ip a
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ns-e19a5a36-a0@if10: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1450 qdisc noqueue state UP group default qlen 1000
    link/ether fa:16:3e:48:b9:33 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.16.1.2/24 brd 172.16.1.255 scope global ns-e19a5a36-a0
       valid_lft forever preferred_lft forever
    inet 169.254.169.254/16 brd 169.254.255.255 scope global ns-e19a5a36-a0
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:fe48:b933/64 scope link 
       valid_lft forever preferred_lft forever
```
root@controller:~# ip netns exec qdhcp-727276b4-2f8b-49f1-a230-b518231d2aac ip a
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ns-9c1a1b3f-d0@if8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default qlen 1000
    link/ether fa:16:3e:00:13:a2 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 169.254.169.254/16 brd 169.254.255.255 scope global ns-9c1a1b3f-d0
       valid_lft forever preferred_lft forever
    inet 10.3.53.200/22 brd 10.3.55.255 scope global ns-9c1a1b3f-d0
       valid_lft forever preferred_lft forever
    inet6 fe80::f816:3eff:fe00:13a2/64 scope link 
       valid_lft forever preferred_lft forever

4. List port router
```
root@controller:~# openstack port list --router router
```
+--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+
| ID                                   | Name | MAC Address       | Fixed IP Addresses                                                         | Status |
+--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+
| 9940e6c7-1351-4245-9631-63f351ac6e55 |      | fa:16:3e:76:71:66 | ip_address='172.16.1.1', subnet_id='67297a50-3847-482c-98f1-6e83239f11f8'  | ACTIVE |
| b033381a-5573-4f76-863e-9eee438b5f8e |      | fa:16:3e:ff:da:f6 | ip_address='10.3.53.205', subnet_id='a45f2349-dbc3-4c62-beec-092e89caef9b' | ACTIVE |
+--------------------------------------+------+-------------------+----------------------------------------------------------------------------+--------+

7. Ping test port
```
ping 10.3.53.205
```
