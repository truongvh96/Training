#### Ansible là gì?
- Ansible là một công cụ automation và orchestration phổ biến, giúp cho chúng ta đơn giản tự động hóa việc triển khai ứng dụng. Nó có thể cấu hình hệ thống, deploy phần mềm
- Ansible có thể tự động hóa việc cài đặt, cập nhật nhiều hệ thống hay triển khai một ứng dụng từ xa

#### Thành phần trong Ansible
Cái này cũng khá nhiều nhưng về cơ bản thì có các phần sau:
- **Playbooks** - Là nơi bạn sẽ khai báo kịch bản chạy cho server
- **Tasks** - Là những công việc nhỏ trong cuốn sổ Playbooks trên
- **Inventory** - Khai báo địa chỉ server cần được setup
- **Modules** - Những chức năng hỗ trợ cho việc thực thi tasks dễ và đang dạng
- **Role** : Một tập hợp các Playbook, các template và các file khác, được tổ chức theo cách được xác định trước để tạo điều kiện tái sử dụng và chia sẻ.
- **Vars**: 
  - Host vars: đặt các biến riêng cho từng host
  - group_vars: đặt các biến chung cho cùng 1 nhóm, ví dụ [webservers] có biến listen_port: 80
