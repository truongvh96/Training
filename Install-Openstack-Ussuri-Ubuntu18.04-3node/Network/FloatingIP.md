Floating IP
Mỗi instance sẽ có 1 Private IP của dải mạng self service để các instance thông tới nhau và 01 Public IP (Floating IP) cho mục đích kết nối từ bên ngoaif internet vào instance chẳng hạn như SSH Pool floating IP được cấu hình bởi admin. Qouta Floating IP được giới hạn theo Project. Các float ip được cấp sau đó các user có thể sử dụng để:

Gắn hoặc xoá floating ip khỏi instance, mỗi instace có duy nhất 1 FLoating IP.
Float IP sẽ được gỡ khi xoá instance và có thể được gắn vào instance khác.
1. Xác thực quyền admin & list subnet
```
# . admin-openrc
# openstack subnet list
```
+--------------------------------------+-------------+--------------------------------------+---------------+
| ID                                   | Name        | Network                              | Subnet        |
+--------------------------------------+-------------+--------------------------------------+---------------+
| 67297a50-3847-482c-98f1-6e83239f11f8 | selfservice | 5e87b8c7-d233-4aaa-9fb3-b9817ff54df1 | 172.16.6.0/24 |
| a45f2349-dbc3-4c62-beec-092e89caef9b | provider    | 727276b4-2f8b-49f1-a230-b518231d2aac | 10.10.10.0/24 |
+--------------------------------------+-------------+--------------------------------------+---------------+
2. Kiểm tra thông tin instace
```
root@controller:~# openstack server show selfservice-instance
```
+-----------------------------+----------------------------------------------------------+
| Field                       | Value                                                    |
+-----------------------------+----------------------------------------------------------+
| OS-DCF:diskConfig           | MANUAL                                                   |
| OS-EXT-AZ:availability_zone | nova                                                     |
| OS-EXT-STS:power_state      | Running                                                  |
| OS-EXT-STS:task_state       | None                                                     |
| OS-EXT-STS:vm_state         | active                                                   |
| OS-SRV-USG:launched_at      | 2021-07-06T04:58:59.000000                               |
| OS-SRV-USG:terminated_at    | None                                                     |
| accessIPv4                  |                                                          |
| accessIPv6                  |                                                          |
| addresses                   | selfservice=172.16.1.31                                  |
| config_drive                |                                                          |
| created                     | 2021-07-06T04:58:47Z                                     |
| flavor                      | m1.nano (0)                                              |
| hostId                      | d7d1917494d6a5adfc33e036d703c276c9d49553ba26a2ae7d0b29a1 |
| id                          | a60459eb-161e-4285-b5ff-e8047987f78b                     |
| image                       | cirros (3967045e-3b9b-4c73-b1ee-97b61cb3f186)            |
| key_name                    | mykey                                                    |
| name                        | selfservice-instance                                     |
| progress                    | 0                                                        |
| project_id                  | 4c7a75c859d3416fa3f6ac1eb58de2b1                         |
| properties                  |                                                          |
| security_groups             | name='default'                                           |
| status                      | ACTIVE                                                   |
| updated                     | 2021-07-06T04:58:59Z                                     |
| user_id                     | fe64d208dfff46a5b938b2ef370eb409                         |
| volumes_attached            |                                                          |
+-----------------------------+----------------------------------------------------------+
3. Tạo floating IP cho project Myproject sử dụng mạng provider
``` 
root@controller:~# openstack floating ip create --project myproject --subnet provider provider
```
+---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field               | Value                                                                                                                                                                     |
+---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| created_at          | 2021-07-06T09:44:14Z                                                                                                                                                      |
| description         |                                                                                                                                                                           |
| dns_domain          | None                                                                                                                                                                      |
| dns_name            | None                                                                                                                                                                      |
| fixed_ip_address    | None                                                                                                                                                                      |
| floating_ip_address | 10.3.53.203                                                                                                                                                               |
| floating_network_id | 727276b4-2f8b-49f1-a230-b518231d2aac                                                                                                                                      |
| id                  | b9ccf90b-7dd7-452e-8f59-53ac4faf9dd8                                                                                                                                      |
| location            | Munch({'cloud': '', 'region_name': '', 'zone': None, 'project': Munch({'id': '4c7a75c859d3416fa3f6ac1eb58de2b1', 'name': None, 'domain_id': None, 'domain_name': None})}) |
| name                | 10.3.53.203                                                                                                                                                               |
| port_details        | None                                                                                                                                                                      |
| port_id             | None                                                                                                                                                                      |
| project_id          | 4c7a75c859d3416fa3f6ac1eb58de2b1                                                                                                                                          |
| qos_policy_id       | None                                                                                                                                                                      |
| revision_number     | 0                                                                                                                                                                         |
| router_id           | None                                                                                                                                                                      |
| status              | DOWN                                                                                                                                                                      |
| subnet_id           | a45f2349-dbc3-4c62-beec-092e89caef9b                                                                                                                                      |
| tags                | []                                                                                                                                                                        |
| updated_at          | 2021-07-06T09:44:14Z                                                                                                                                                      |
+---------------------+---------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
3. Kiểm tra floating ip vừa tạo
```
root@controller:~# openstack floating ip list
```
+--------------------------------------+---------------------+------------------+------+--------------------------------------+----------------------------------+
| ID                                   | Floating IP Address | Fixed IP Address | Port | Floating Network                     | Project                          |
+--------------------------------------+---------------------+------------------+------+--------------------------------------+----------------------------------+
| b9ccf90b-7dd7-452e-8f59-53ac4faf9dd8 | 10.3.53.203         | None             | None | 727276b4-2f8b-49f1-a230-b518231d2aac | 4c7a75c859d3416fa3f6ac1eb58de2b1 |
+--------------------------------------+---------------------+------------------+------+--------------------------------------+----------------------------------+
4. Lấy thông tin Instance name
```
root@controller:~# openstack server list
```
+--------------------------------------+----------------------+--------+-------------------------+--------+---------+
| ID                                   | Name                 | Status | Networks                | Image  | Flavor  |
+--------------------------------------+----------------------+--------+-------------------------+--------+---------+
| a60459eb-161e-4285-b5ff-e8047987f78b | selfservice-instance | ACTIVE | selfservice=172.16.1.31 | cirros | m1.nano |
| 6848477a-9914-4e1b-87bf-591386c545de | provider-instance    | ACTIVE | provider=10.3.53.209    | cirros | m1.nano |
+--------------------------------------+----------------------+--------+-------------------------+--------+---------+
5. Add floating IP vào instance
```
root@controller:~# openstack server add floating ip selfservice-instance 10.10.10.2
```
6. Kiểm tra lại thông tin instance xem đã mapping ip chưa
```
root@controller:~# openstack server show selfservice-instance
```
+-----------------------------+----------------------------------------------------------+
| Field                       | Value                                                    |
+-----------------------------+----------------------------------------------------------+
| OS-DCF:diskConfig           | MANUAL                                                   |
| OS-EXT-AZ:availability_zone | nova                                                     |
| OS-EXT-STS:power_state      | Running                                                  |
| OS-EXT-STS:task_state       | None                                                     |
| OS-EXT-STS:vm_state         | active                                                   |
| OS-SRV-USG:launched_at      | 2021-07-06T04:58:59.000000                               |
| OS-SRV-USG:terminated_at    | None                                                     |
| accessIPv4                  |                                                          |
| accessIPv6                  |                                                          |
| addresses                   | selfservice=172.16.1.31, 10.3.53.203                     |
| config_drive                |                                                          |
| created                     | 2021-07-06T04:58:47Z                                     |
| flavor                      | m1.nano (0)                                              |
| hostId                      | d7d1917494d6a5adfc33e036d703c276c9d49553ba26a2ae7d0b29a1 |
| id                          | a60459eb-161e-4285-b5ff-e8047987f78b                     |
| image                       | cirros (3967045e-3b9b-4c73-b1ee-97b61cb3f186)            |
| key_name                    | mykey                                                    |
| name                        | selfservice-instance                                     |
| progress                    | 0                                                        |
| project_id                  | 4c7a75c859d3416fa3f6ac1eb58de2b1                         |
| properties                  |                                                          |
| security_groups             | name='default'                                           |
| status                      | ACTIVE                                                   |
| updated                     | 2021-07-06T04:58:59Z                                     |
| user_id                     | fe64d208dfff46a5b938b2ef370eb409                         |
| volumes_attached            |                                                          |
+-----------------------------+----------------------------------------------------------+
7. Kiểm tra kết nối tới Float IP
```
root@controller:~# ping 10.10.10.2
```
PING 10.3.53.203 (10.3.53.203) 56(84) bytes of data.
64 bytes from 10.3.53.203: icmp_seq=1 ttl=63 time=3.71 ms
64 bytes from 10.3.53.203: icmp_seq=2 ttl=63 time=1.61 ms
--- 10.3.53.203 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 1.614/2.664/3.715/1.051 ms
8. SSH TEST
```
root@controller:~# ssh cirros@10.10.10.2
```
The authenticity of host '10.3.53.203 (10.3.53.203)' can't be established.
ECDSA key fingerprint is SHA256:FbFPSwhu03IVU2clwzWpnYhdVaO1e9NR1KQPBCH9irE.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '10.3.53.203' (ECDSA) to the list of known hosts.
$ 
9. Kiểm tra lại Float ip
```
root@controller:~# openstack floating ip list
```
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
| ID                                   | Floating IP Address | Fixed IP Address | Port                                 | Floating Network                     | Project                          |
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
| b9ccf90b-7dd7-452e-8f59-53ac4faf9dd8 | 10.3.53.203         | 172.16.1.31      | f43cb5a7-bdd6-45a7-9740-a02d9b31a067 | 727276b4-2f8b-49f1-a230-b518231d2aac | 4c7a75c859d3416fa3f6ac1eb58de2b1 |
+--------------------------------------+---------------------+------------------+--------------------------------------+--------------------------------------+----------------------------------+
10. Xoá IP khỏi instance
```
$ openstack server remove floating ip selfservice-instance 10.10.10.2
```
