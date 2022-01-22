# Endpoint và Catalog
## **1) Endpoint**
- **Endpoint** trong **Keystone** là một URL có thể được sử dụng để truy cập dịch vụ trong **Openstack** .
- Một **endpoint** giống như một điểm liên lạc để người dùng sử dụng dịch vụ của **OpenStack**.
- Có 3 loại **endpoint** :
    - `adminurl` : dùng cho user quản trị
    - `internalurl`: là những gì các dịch vụ khác sử dụng để giao tiếp với nhau
    - `publicurl` : là nơi các user khác truy cập và sử dụng dịch vụ
- Hiển thị list các **endpoint** :
    ```
    # openstack endpoint list
    ```
```
+----------------------------------+-----------+--------------+--------------+---------+-----------+-----------------------------+
| ID                               | Region    | Service Name | Service Type | Enabled | Interface | URL                         |
+----------------------------------+-----------+--------------+--------------+---------+-----------+-----------------------------+
| 1c015cfcfd18454180dd1860dc3c9fe6 | RegionOne | glance       | image        | True    | admin     | http://controller:9292      |
| 24da5a292bf84a4cb422979343d7fe98 | RegionOne | placement    | placement    | True    | internal  | http://controller:8778      |
| 2a8fa6f5f436489b9679a20d1dbc2d74 | RegionOne | keystone     | identity     | True    | admin     | http://controller:5000/v3/  |
| 6109b917415642e392ff8af417a2a83e | RegionOne | neutron      | network      | True    | public    | http://controller:9696      |
| 6b60d151d2a443ffa0eee31b3fb1bb17 | RegionOne | glance       | image        | True    | internal  | http://controller:9292      |
| 7374ccf2274648d8af6ddeaaa741ea67 | RegionOne | glance       | image        | True    | public    | http://controller:9292      |
| 73d4ed5248d14b3cafa184017c46aab9 | RegionOne | neutron      | network      | True    | internal  | http://controller:9696      |
| 774b4f4ce4214284acf6bf98cd209791 | RegionOne | placement    | placement    | True    | admin     | http://controller:8778      |
| 7782b5262dcd4650b73f6431380c2a6f | RegionOne | keystone     | identity     | True    | internal  | http://controller:5000/v3/  |
| 885eebcad9b641f299373c461ec5a72f | RegionOne | nova         | compute      | True    | public    | http://controller:8774/v2.1 |
| 9ee7c985f7c84bb6900ded8342d4df32 | RegionOne | nova         | compute      | True    | admin     | http://controller:8774/v2.1 |
| b6d1c35a37b1427895e3e80c880020c8 | RegionOne | placement    | placement    | True    | public    | http://controller:8778      |
| ba56a025e0104e83a211065a888ed31c | RegionOne | nova         | compute      | True    | internal  | http://controller:8774/v2.1 |
| ddf438e7486d4b02803d0f878afc9840 | RegionOne | keystone     | identity     | True    | public    | http://controller:5000/v3/  |
| ea70c0b0a932434699adbf0fd9907d9c | RegionOne | neutron      | network      | True    | admin     | http://controller:9696      |
+----------------------------------+-----------+--------------+--------------+---------+-----------+-----------------------------+
```

- Hiển thị chi tiết một **endpoint** :
    ```
    # openstack show endpoint <endpoint_id>
    ```
```
+--------------+----------------------------------+
| Field        | Value                            |
+--------------+----------------------------------+
| enabled      | True                             |
| id           | 1c015cfcfd18454180dd1860dc3c9fe6 |
| interface    | admin                            |
| region       | RegionOne                        |
| region_id    | RegionOne                        |
| service_id   | 750a0fbadb12445ba0f37f14c02f24c9 |
| service_name | glance                           |
| service_type | image                            |
| url          | http://controller:9292           |
+--------------+----------------------------------+
```

## **2) Catalog**
- **Catalog** trong **OpenStack** là tập hợp các danh mục sẵn sàng sử dụng mà user có thể sử dụng trong **OpenStack**.
- **Service catalog** cung cấp cho người dùng về các dịch vụ có sẵn trong **OpenStack**, cùng với thông tin bổ sung về các vùng, phiên bản API và các project có sẵn.
- **Catalog** làm cho việc tìm kiếm dịch vụ hiệu quả, chẳng hạn như cách định cấu hình liên lạc giữa các dịch vụ.
- Hiển thị list **catalog** :
    ```
    # openstack catalog list
    ```
```
+-----------+-----------+-----------------------------------------+
| Name      | Type      | Endpoints                               |
+-----------+-----------+-----------------------------------------+
| neutron   | network   | RegionOne                               |
|           |           |   public: http://controller:9696        |
|           |           | RegionOne                               |
|           |           |   internal: http://controller:9696      |
|           |           | RegionOne                               |
|           |           |   admin: http://controller:9696         |
|           |           |                                         |
| glance    | image     | RegionOne                               |
|           |           |   admin: http://controller:9292         |
|           |           | RegionOne                               |
|           |           |   internal: http://controller:9292      |
|           |           | RegionOne                               |
|           |           |   public: http://controller:9292        |
|           |           |                                         |
| placement | placement | RegionOne                               |
|           |           |   internal: http://controller:8778      |
|           |           | RegionOne                               |
|           |           |   admin: http://controller:8778         |
|           |           | RegionOne                               |
|           |           |   public: http://controller:8778        |
|           |           |                                         |
| keystone  | identity  | RegionOne                               |
|           |           |   admin: http://controller:5000/v3/     |
|           |           | RegionOne                               |
|           |           |   internal: http://controller:5000/v3/  |
|           |           | RegionOne                               |
|           |           |   public: http://controller:5000/v3/    |
|           |           |                                         |
| nova      | compute   | RegionOne                               |
|           |           |   public: http://controller:8774/v2.1   |
|           |           | RegionOne                               |
|           |           |   admin: http://controller:8774/v2.1    |
|           |           | RegionOne                               |
|           |           |   internal: http://controller:8774/v2.1 |
|           |           |                                         |
+-----------+-----------+-----------------------------------------+
```

- Hiển thị chi tiết một mục trong **catalog** :
    ```
    # openstack catalog show <catalog_name>
    ```
```
+-----------+------------------------------------+
| Field     | Value                              |
+-----------+------------------------------------+
| endpoints | RegionOne                          |
|           |   admin: http://controller:9292    |
|           | RegionOne                          |
|           |   internal: http://controller:9292 |
|           | RegionOne                          |
|           |   public: http://controller:9292   |
|           |                                    |
| id        | 750a0fbadb12445ba0f37f14c02f24c9   |
| name      | glance                             |
| type      | image                              |
+-----------+------------------------------------+
```
