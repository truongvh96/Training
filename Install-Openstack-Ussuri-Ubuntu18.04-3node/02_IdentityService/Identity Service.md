Identity Service
OpenStack Identity service cung cấp điểm để quản lý xác thực, uỷ quyền user và danh mục dịch vụ. Có thể được tích hợp với LDAP. Đây là dịch vụ đầu tiên được sử dụng để tương tác với User.

Install & Config
## 1. Database cho Keystone
Truy cập Mariadb:
``` mysql -u root -p ```
Tạo DB:
```
CREATE DATABASE keystone;
Gán quyền truy cập
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost'  IDENTIFIED BY 'Welcome123';
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'Welcome123';
 FLUSH PRIVILEGES;
```
2. Cài đặt và cấu hình

Cài đặt Package
```
apt install keystone
```
Cấu hình sửa file /etc/keystone/keystone.conf thêm các dòng: ``` nano /etc/keystone/keystone.conf  ```
```
[database]
connection = mysql+pymysql://keystone:Welcome123@controller/keystone #Welcome123 là pass
[token]
provider = fernet
```
Insert vào DB có thể kiểm tra quá trình với file log keystone /var/log/keystone/keystone-manage.log
```
su -s /bin/sh -c "keystone-manage db_sync" keystone
```
3. Tạo Fernet key repository
```
# keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
# keystone-manage credential_setup --keystone-user keystone --keystone-group keystone
```
4. Bootstrap Identity service
```
keystone-manage bootstrap --bootstrap-password Welcome123 \
  --bootstrap-admin-url http://controller:5000/v3/ \
  --bootstrap-internal-url http://controller:5000/v3/ \
  --bootstrap-public-url http://controller:5000/v3/ \
  --bootstrap-region-id RegionOne
```
Welcome123: là password của user admin
5. Cấu hình Apache

Sửa file ``` nano /etc/apache2/apache2.conf ``` thêm dòng sau: 
```
ServerName controller:
```
Restart Apache:
```
service apache2 restart
```
6. Cấu hình admin account
```
$ export OS_USERNAME=admin
$ export OS_PASSWORD=Welcome123@ #password
$ export OS_PROJECT_NAME=admin
$ export OS_USER_DOMAIN_NAME=Default
$ export OS_PROJECT_DOMAIN_NAME=Default
$ export OS_AUTH_URL=http://controller:5000/v3
$ export OS_IDENTITY_API_VERSION=3
```
Tạo Script environment cho Admin
```
cat <<EOT >> admin-openrc
export OS_PROJECT_DOMAIN_NAME=Default
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_NAME=admin
export OS_USERNAME=admin
export OS_PASSWORD=Welcome123  #password
export OS_AUTH_URL=http://controller:5000/v3
export OS_IDENTITY_API_VERSION=3
export OS_IMAGE_API_VERSION=2
EOT
```
Sử dụng script xác thực
```
# source admin-openrc
```
Check hoạt động:
``` # openstack token issue ```
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                                                                   |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| expires    | 2021-07-01T08:36:31+0000                                                                                                                                                                |
| id         | gAAAAABg3XB_k0UaIxPx-AjJKicsL1flPODPRIgLtAApciWN9bU_VX1Y-jIO1_pwnvsNPSzvog7Lg8NBr01_alXVaHiW7MXqtkJQLYsmoCgbWPeHutrIAt3_FV7tXjJ4ODer2gZGKcE9K9H4cg2pBH3aJrpzeiQJTx6HJKnLuy_moBVPRLA1y5c |
| project_id | 4c0116afd59f49a8a40b3346b91d5ffc                                                                                                                                                        |
| user_id    | a29c10926d034ca48038b057f32f8eb2                                                                                                                                                        |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
7. Tạo Domain, Project, User & Role

Create Domain new
``` # openstack domain create --description "new" example ```
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | new                              |
| enabled     | True                             |
| id          | 1d01d82248eb4db8bcfc3d1b93e090f1 |
| name        | example                          |
| options     | {}                               |
| tags        | []                               |
+-------------+----------------------------------+
Tạo Project mới:
``` # openstack project create --domain default   --description "Demo Project" truongproject ``` #truongproject name project
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | Demo Project                     |
| domain_id   | default                          |
| enabled     | True                             |
| id          | 48b1f7ba845a4056879d4a81d411362b |
| is_domain   | False                            |
| name        | truongproject                    |
| options     | {}                               |
| parent_id   | default                          |
| tags        | []                               |
+-------------+----------------------------------+
Tạo user non-admin cho project mới tạo:
```
# openstack user create --domain default \
>   --password-prompt truong
```
Yếu cầu nhập pass:
User Password:
Repeat User Password:
+---------------------+----------------------------------+
| Field               | Value                            |
+---------------------+----------------------------------+
| domain_id           | default                          |
| enabled             | True                             |
| id                  | fbf4069d966240a7a96bff24dbfc6244 |
| name                | truong                           |
| options             | {}                               |
| password_expires_at | None                             |
+---------------------+----------------------------------+
Tạo Role:
``` # openstack role create truong ```
+-------------+----------------------------------+
| Field       | Value                            |
+-------------+----------------------------------+
| description | None                             |
| domain_id   | None                             |
| id          | d3fb7de6e59b467a8cb1a9b32071dd78 |
| name        | truong                           |
| options     | {}                               |
+-------------+----------------------------------+

Add role cho project truongproject và user truong
``` # openstack role add --project truongproject --user truong truong ```
8. Test

User admin
```
root@controller:~# openstack --os-auth-url http://controller:5000/v3 \
>   --os-project-domain-name Default --os-user-domain-name Default \
>   --os-project-name admin --os-username admin token issue
```
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                                                                   |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| expires    | 2021-07-01T08:48:21+0000                                                                                                                                                                |
| id         | gAAAAABg3XNFBZR4VNmgytNHPkcl2f7PuUhFep0atf1vM4HrYXMTNqk-r7umSMQGXZYpYXq_Z73_jM6hp4VLcVYQoUI-4BEnRqP835ajyTtJu8ADJDEQpbmtp_vtKfEhkcOZzXDLlfZgXlxsF_KmKmLUu9KJpfga03qZzFAfljJ805_GW9APatQ |
| project_id | 4c0116afd59f49a8a40b3346b91d5ffc                                                                                                                                                        |
| user_id    | a29c10926d034ca48038b057f32f8eb2                                                                                                                                                        |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
User truong
```
root@controller:~# openstack --os-auth-url http://controller:5000/v3 \
>   --os-project-domain-name Default --os-user-domain-name Default \
>   --os-project-name truongproject --os-username truong token issue
```
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| Field      | Value                                                                                                                                                                                   |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
| expires    | 2021-07-01T08:51:21+0000                                                                                                                                                                |
| id         | gAAAAABg3XP5onRq_se015lEenhNtRQLL0spqQyb0ilPjqkdigMH9IPr8HBsLN25dTRbHsyU27-AmByp7CPLlihw2JYS_RNUCpAIQjTOqJ04FWQYkB6Sg2y7G03m4-VAEZDo-rPmwZPpUH9-76qZDJNd81ELGnk74D2ZSEEArDhdobGUAl8Mefw |
| project_id | 48b1f7ba845a4056879d4a81d411362b                                                                                                                                                        |
| user_id    | fbf4069d966240a7a96bff24dbfc6244                                                                                                                                                        |
+------------+-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------+
