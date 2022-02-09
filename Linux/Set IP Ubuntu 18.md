Cấu hình IP cho Ubuntu 18.04 sử dụng Netplan
#### 1. Cấu hình Static IP
Sửa file cấu hình tại đường dẫn : ``` /etc/netplan/00-network-manager-all.yaml ```
Sửa file và thêm các thông tin:
```
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    ens33:
      dhcp4: no
      addresses:
        - 172.16.6.122/24
      gateway4: 172.16.6.254
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
Apply config
```
```
# sudo netplan apply
```

#### 2. Cấu hình Dynamic IP
Sửa file cấu hình tại đường dẫn : ``` /etc/netplan/01-network-manager-all.yaml ```
Sửa file và thêm các thông tin:
```
network:
  version: 2
  renderer: NetworkManager
  ethernets:
    ens33:
      dhcp4: yes
Apply config
```
```
# sudo netplan apply
```
