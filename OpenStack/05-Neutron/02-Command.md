# Các command thường sử dụng trong Neutron
## **1) Network**
#### **List các network**
- Cú pháp :
    ```
    # openstack network list
    ```
```
+--------------------------------------+---------+--------------------------------------+
| ID                                   | Name    | Subnets                              |
+--------------------------------------+---------+--------------------------------------+
| 9abfcc47-4958-44a5-86f4-e25daabc9edc | private | d39186aa-b5a1-486d-b36e-262d3c699271 |
| c20d8cdd-a81f-492a-9af9-021036d2c02a | public  | 8e647de3-bb01-45ed-ad95-9db57b5f6ae6 |
+--------------------------------------+---------+--------------------------------------+
```

#### **List các subnet**
- Cú pháp :
    ```
    # openstack subnet list
    ```
```
+--------------------------------------+-------------+--------------------------------------+---------------+
| 8e647de3-bb01-45ed-ad95-9db57b5f6ae6 | sub_public  | c20d8cdd-a81f-492a-9af9-021036d2c02a | 172.16.6.0/24 |
| d39186aa-b5a1-486d-b36e-262d3c699271 | sub_private | 9abfcc47-4958-44a5-86f4-e25daabc9edc | 10.10.10.0/24 |
+--------------------------------------+-------------+--------------------------------------+---------------+
```
#### **Tạo network mới**
- Tạo một network provider :
    ```
    # openstack network create \
    --share --external --project-domain <project_name> \
    --provider-physical-network <provider_net_name> \
    --provider-network-type <net_type> <net_name>
    ```
    - **VD :**
        ```
        # openstack network create \
        --share --external --project-domain admin \
        --provider-physical-network provider \
        --provider-network-type flat public
        ```
- Tạo một network self-service :
    ```
    # openstack network create --project-domain <project_name> <net_name>
    ```
    - **VD :**
        ```
        # openstack network create --project-domain admin private2
        ```
```
| ipv6_address_scope        | None
              |
| is_default                | False
              |
| is_vlan_transparent       | None
              |
| location                  | cloud='', project.domain_id=, project.domain_name='Default', project.id='69a1f7b2edf64556a214fd979305a879', project.name='admin', region_name='', zone= |
| mtu                       | 1450
              |
| name                      | private2
              |
| port_security_enabled     | True
              |
| project_id                | 69a1f7b2edf64556a214fd979305a879
              |
| provider:network_type     | vxlan
              |
| provider:physical_network | None
              |
| provider:segmentation_id  | 2
              |
| qos_policy_id             | None
              |
| revision_number           | 1
              |
| router:external           | Internal
              |
| segments                  | None
              |
| shared                    | False
              |
| status                    | ACTIVE
              |
| subnets                   |
              |
| tags                      |
              |
| updated_at                | 2022-01-23T15:28:26Z
              |
+---------------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+
```

        - Lưu ý :
            - **port security** mặc định được bật (disable bằng option `--disable-port-security`)
            - network type mặc định là `vxlan`
            - private network sẽ ở mode `Internal` thay vì `External` như provider
            - Không dùng tùy chọn `share`
#### **Tạo subnet**
- Tại đây, tạo dải mạng, default gateway, DNS cho network
    > **Lưu ý:** đối với **external network** thì dải mạng khai báo phải trùng với dải provider để máy ảo ra ngoài.
- Cú pháp :
    ```
    # openstack subnet create --network <net_name> \
    --allocation-pool start=<start_IP>,end=<end_IP> \
    --dns-nameserver <DNS_server> --gateway <IP_gateway> \
    --subnet-range <range/subnet_mask> \
    <subnet_name>
    ```
- **VD :**
    ```
    # openstack subnet create --network private2 \
    --allocation-pool start=172.16.6.100,end=172.16.6.200 \
    --dns-nameserver 8.8.8.8 --gateway 172.16.6.1 \
    --subnet-range 172.16.6.0/24 \
    sub1private2
    ```
```
| description       |
      |
| dns_nameservers   | 8.8.8.8
      |
| enable_dhcp       | True
      |
| gateway_ip        | 172.16.6.1
      |
| host_routes       |
      |
| id                | 5f395667-dae5-4840-bbe7-409eab246fa7
      |
| ip_version        | 4
      |
| ipv6_address_mode | None
      |
| ipv6_ra_mode      | None
      |
| location          | cloud='', project.domain_id=, project.domain_name='Default', project.id='69a1f7b2edf64556a214fd979305a879', project.name='admin', region_name='',
zone= |
| name              | sub1private2
      |
| network_id        | c4403bb4-6b8f-4c8a-a012-78b921693500
      |
| prefix_length     | None
      |
| project_id        | 69a1f7b2edf64556a214fd979305a879
      |
| revision_number   | 0
      |
| segment_id        | None
      |
| service_types     |
      |
| subnetpool_id     | None
      |
| tags              |
      |
| updated_at        | 2022-01-23T15:30:46Z
      |
+-------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------+
```
#### **Show thông tin chi tiết một network**
- Cú pháp :
    ```
    # openstack network show <net_name>
    ```
- **VD :** 
    ```
    # openstack network show public
    ```

#### **Show thông tin chi tiết một subnet**
- Cú pháp :
    ```
    # openstack subnet show <subnet_name>
    ```
- **VD :**
    ```
    # openstack subnet show sub1private2
    ```

#### **Xóa network**
- Cú pháp :
    ```
    # openstack network delete <network_name|network_ID>
    ```
#### **Xóa subnet**
- Cú pháp :
    ```
    # openstack subnet delete <subnet_name|subnet_ID>
    ```
## **2) Cấu hình IP cho instance**
#### **List các port đang có trên instance**
- Cú pháp :
    ```
    # openstack port list --server <instance_name>
    ```
- **VD :**
    ```
    # openstack port list --server truongvh
    ```
```
+--------------------------------------+------+-------------------+-----------------------------------------------------------------------------+--------+
| ID                                   | Name | MAC Address       | Fixed IP Addresses                                                          | Status |
+--------------------------------------+------+-------------------+-----------------------------------------------------------------------------+--------+
| 2e459de7-78ea-4579-8e48-72aaedaacbe5 |      | fa:16:3e:f7:2f:06 | ip_address='172.16.6.123', subnet_id='8e647de3-bb01-45ed-ad95-9db57b5f6ae6' | ACTIVE |
+--------------------------------------+------+-------------------+-----------------------------------------------------------------------------+--------+
```

#### **Gán IP mới cho instance**
- **B1 :** Ngắt kết nối và xóa port đang gắn với instance :
    ```
    # nova interface-detach <instance_name> <port_ID>
    ```
    - **VD :**
        ```
        # nova interface-detach trungvh 2e459de7-78ea-4579-8e48-72aaedaacbe5
        ```
- **B2 :** Tạo 1 port mới :
    - Lấy theo DHCP :
        ```
        # openstack port create --fixed-ip subnet=<subnet_name> --network <net_name> <port_name>
        ```
    - Gán IP tĩnh :
        ```
        # openstack port create --fixed-ip subnet=<subnet_name>,ip-address=<IP>  --network <net_name> <port_name>
        ```
        > Chú ý : Kiểm tra kỹ địa chỉ IP đã được sử dụng chưa, tránh conflict IP
    - **VD :**
        ```
        # openstack port create --fixed-ip subnet=sub1private1,ip-address=192.168.6.125 --network private01 port01
        ```

        > Copy lại `port_ID` tạo ra cho bước tiếp theo
- **B3 :** Gán port cho instance :
    ```
    # nova interface-attach --port-id <port_ID> <instance_name>
    ```
    - **VD :**
        ```
        # nova interface-attach --port-id 8cf718d7-ed15-410c-aca2-4b6184d9e47d VM05
        ```
- **B4 :** Khởi động lại instance :
    ```
    # openstack server reboot <instance_name>
    ```
- **B5 :** Console instance và kiểm tra IP đã nhận chưa :

    > Instance đã nhận đúng IP : ``` ip a  ```

    > Instance có thể ping đến các IP khác : ``` ping ip ```

