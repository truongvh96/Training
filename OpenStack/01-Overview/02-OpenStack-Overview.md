#### 1) Giới thiệu
- OpenStack là nền tảng mã nguồn mở, được sử dụng để xây dựng mô hình private cloud và public cloud .
- Chính xác hơn, OpenStack là một hệ điều hành cloud có thể kiểm soát lượng lớn các compute, storage và tài nguyên mạng như một Data Center, tất cả thông qua API với các cơ chế xác thực phổ biến .
- Lịch sử hình thành :
Amazone Web Service (AWS) là nguồn cảm hứng ra đời cho OpenStack.
OpenStack được sáng lập bởi NASA và RACKSPACE năm 2010. NASA đóng góp Nebula project, sau có tên là NOVA như ngày nay, RACKSPACE đóng góp SWIFT project – project về lưu trữ file.
Ngày nay , project OpenStack đã có sự tham gia của nhiều “ông lớn” như : AT&T, Ubuntu, IBM, RedHat, SUSE, Mirantis,...
- OpenStack được thiết kế theo hướng module. OpenStack là một project lớn là sự kết hợp của các project thành phần: nova, swift, neutron, glance, etc.
- Mở về: Thiết kế/ Phát triển/ Cộng đồng/ Mã nguồn.
- 99.99% mã nguồn được viết bằng Python 2.x
- Chu kì 6 tháng sẽ cho ra một phiên bản mới.
- Tên các phiên bản được đánh theo A, B, C (Austin, Bexar, Cactus, etc.)…. Tối đa 10 kí tự là danh từ.
- Phiên bản ổn định mới nhất : Xena (ra mắt ngày 2021-10-06)
- Phiên bản đang phát triển : Yoga ( dự kiến ra mắt ngày 2022-03-30).
- OpenStack nhắm đến mục đích thực hiện đơn giản, khả năng mở rộng lớn và một tính năng phong phú.
- OpenStack cung cấp giải pháp IaaS thông qua nhiều dịch vụ bổ sung. Mỗi dịch vụ cung cấp API tạo điều kiện cho việc tích hợp này. Thêm vào đó OpenStack cũng cung cấp thêm một màn dashboard hỗ trợ cho quản trị viên trong việc cung cấp tài nguyên cho người dùng qua giao diện web .
![image](https://user-images.githubusercontent.com/97424062/149957755-44714c98-4418-4b61-a0f2-302171534c48.png)

#### 2) Kiến trúc OpenStack
Kiến trúc mức khái niệm :
![image](https://user-images.githubusercontent.com/97424062/149957620-93892caf-aa47-414b-b96a-22930d42a6a1.png)

Kiến trúc mức logic :
![image](https://user-images.githubusercontent.com/97424062/149957571-ad93994d-0de1-4621-90dd-8453841e639e.png)

#### 3) Các thành phần trong OpenStack
- Có thể coi OpenStack như một hệ điều hành cloud có nhiệm vụ kiểm soát các tài nguyên tính toán(compute), lưu trữ(storage) và networking trong hệ thống lớn datacenter, tất cả đều có thể được kiểm soát qua giao diện dòng lệnh hoặc một dashboard( do project Horizon cung cấp). OpenStack có 6 core project. 6 core project của OpenStack bao gồm: **Nova, Neutron, Swift, Cinder, Keystone, Glance.**
![image](https://user-images.githubusercontent.com/97424062/149957506-11fb8b3a-22a1-4ace-bd7a-58117c26e258.png)

##### 3.1) Keystone – Identity Service
- Cung cấp dịch vụ xác thực và ủy quyền cho các dịch vụ khác của OpenStack, cung cấp danh mục của các endpoints cho tất các dịch vụ trong OpenStack. Cụ thể hơn:
- Xác thực user và vấn đề token để truy cập vào các dịch vụ
- Lưu trữ user và các tenant cho vai trò kiểm soát truy cập(cơ chế role-based access control - RBAC)
- Cung cấp catalog của các dịch vụ (và các API enpoints của chúng) trên cloud
- Tạo các policy giữa user và dịch vụ
##### 3.2) Glance - Image Service
- Quản lý các disk image ảo
- Hỗ trợ các image .raw, Hyper-V (.vhd), VirtualBox (.vdi), Qemu (.qcow2) và VMWare (.vmdk, .ovf).
- Người dùng có thể thực hiện: cập nhật thêm các virtual disk images, cấu hình các public và private image và điều khiển việc truy cập, tạo và xóa chúng.
##### 3.3) Nova – Compute
- Là module quản lý và cung cấp máy ảo.
- Nova hỗ trợ nhiều hypervisors gồm KVM, QEMU, LXC, XenServer...
- Là một công cụ mạnh mẽ mà có thể điều khiển toàn bộ các công việc: networking, CPU, storage, memory, tạo, điều khiển và xóa bỏ máy ảo, security, access control. Có thể điều khiển tất cả bằng lệnh hoặc từ giao diện dashboard trên web.
##### 3.4) Neutron – Network Service
- Là thành phần quản lý network cho các máy ảo.
- Cung cấp chức năng network as a service.
- Đây là hệ thống có các tính chất pluggable, scalable và API-driven.
##### 3.5) Cinder - Block Storage
- Cung cấp các storage để chạy máy ảo. Kiến trúc linh hoạt của nó cho phép người dùng khởi tạo và quản lí các thiết bị lưu trữ.
##### 3.6) Horizon - Dashboard
- Cung cấp cho người quản trị cũng như người dùng giao diện đồ họa để truy cập, cung cấp và tự động tài nguyên cloud.
- Việc thiết kế có thể mở rộng giúp dễ dàng thêm vào các sản phẩm cũng như dịch vụ ngoài như billing, monitoring và các công cụ giám sát khác.
