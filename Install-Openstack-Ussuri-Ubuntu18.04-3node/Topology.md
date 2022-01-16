# Cài đặt Openstack Ussuri Release Minimum trên Ubuntu 18.04.6

1\. Mô hình triển khai

Controller Node (8 core, 4 GB, 2 NIC)**

- Host name: controller
- IP mgmt /Storage Network: 10.0.0.2/24
- SSH: 172.16.6.71/24
- Network Provider: 172.16.6.71/24
- Gateway: 172.16.6.1

Compute Node (8 core , 4 GB, 2 NIC)**

- Host name: compute
- IP mgmt /Storage Network: 10.10.10.40/24
- SSH: 172.16.6.70/24
- Network Provider: 172.16.6.70/24
- Gateway: 172.16.6.1

Block Storage (4 Core, 4 GB, 2 NIC)

- Host name: block
- IP mgmt/ Storage Network: 10.10.10.39/24
- SSH: 172.16.6.69/24

2\. Các thành phần được cài đặt

A. Controller Node

1\. Necessary Service**

- [ ] Database Service : MariaDB
- [ ] NoSQL Database Service: Etcd
- [ ] Message Queue: RabbitMQ
- [ ] Network Time Service: NTP
- [ ] Identity Service: Keystone
- [ ] Image Service: Glance
- [ ] Placement
- [ ] Compute Managerment: Nova Mgmt

2\. Network Service**

- [ ] Network Mgmt: Neutron Mgmt
- [ ] Ml2-Plugin
- [ ] Linux Network Utilities
- [ ] DHCP Agent
- [ ] Linux Bridge Agent
- [ ] L3 Agent
- [ ] Medata Agent

3\. Storage Service**

- [ ] Block Storage Mgmt
- [ ] Orchestration
- [ ] Object Storage Proxy Service
- [ ] Shared File System Mgmt
- [ ] Database Mgmt
- [ ] Telementry Mgmg
- [ ] Telementry Agent

B. Compute Node

- [ ] KVM Hypervisor
- [ ] Compute Service
- [ ] Linux Network Utilities
- [ ] Linux Bridge Agent
- [ ] Telementry Agent

C. Block Storage Node

- [ ] ISCSI Target Service
- [ ] Block Storage Volume Service
- [ ] Linux Network Utilities
- [ ] Linux Bridge Agent
- [ ] Telementry Agent
