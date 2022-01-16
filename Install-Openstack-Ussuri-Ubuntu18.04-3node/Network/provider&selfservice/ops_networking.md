Option 1: Provider Network
Provider network cung cấp các dịch vụ network layer 2 (bridge, switching). Một switch bridge được tạo để link tới interface vật lý, và sau đó dùng để gắn interface ảo trên instance. Nghĩa là 1 nic ảo sẽ được tạo và kết nối tới nic vật lý sử dụng virtual switch được tạo từ linux bridge hoặc open vswitch. Mạng nãy cần có DHCP server để provider cấp network cho instance

Các thành phần cần cài đặt trên Network Node:

neutron server: Cấu hình và quản lý network cho Openstack, các thông tin được lưu vào db, queue phục vụ cho việc truy vấn, trích xuất, cấp thông tin network khi có request từ instance. Kết nối tới keystone service và nova compute service.
neutron-plugin-ml2: Plugin sử dụng cho cơ chế bridge của Linux phục vụ xây dựng layer 2 network.
neutron-linuxbridge-agent: Cấu hình kết nối layer2 và sercurity group.
neutron-dhcp-agent: Cung cấp dhcp services cho virtual network.
neutron-metadata-agent: được cung cấp thông tin về cấu hình, metadata để instace trích xuất (vd: instance credentials, flavor, keypair) qua địa chỉ 169.254.168.254
Các thành phần cần cài đặt trên Compute Node:

neutron-linuxbridge-agent: Cấu hình kết nối layer2 và sercurity group.
Mô hình

https://github.com/truongvh96/Openstack/blob/main/Install-Openstack-Ussuri-Ubuntu18.04-3node/Network/provider&selfservice/network1-connectivity.png?raw=true

Trên Controller Node (Network Node):

01 Bridge Provider được tạo kết nối tới Interface 2 (NIC vật lý) và port tap kết nối tới instance
01 DHCP Network Namespace được kết nối tới Bridge Provider phục vụ cho việc cấp IP động cho Instance.
Trên Compute Node :

01 Bridge Provider được cấu hình để map interface 2 (NIC Vật lý),
01 Port tap dùng để kết nối tới Virtual NIC trên Instance.
Option 2: Self Service Network
Mạng này cung cấp kết nối layer 3 (routing, NAT). Các mạng ảo sẽ được định tuyến sử dụng một Virtual Router, kết nối tới mạng vật lý qua giao thứ NAT. User có thể tạo môi trường mạng ảo theo ý muốn và mapping với mạng vật lý để instace kết nội được internet Với mạng này để instace có thể được truy cập từ bên ngoài sẽ cần có Floating IP cho instace. Tương tự mạng này cũng cần DHCP server để cấp IP cho instance

Các thành phần cần cài đặt trên Network Node:

neutron server: Cấu hình và quản lý network cho Openstack, các thông tin được lưu vào db, queue phục vụ cho việc truy vấn, trích xuất, cấp thông tin network khi có request từ instance. Kết nối tới keystone service và nova compute service.
neutron-plugin-ml2: Plugin sử dụng cho cơ chế bridge của Linux phục vụ xây dựng layer 2 network.
neutron-linuxbridge-agent: Cấu hình kết nối layer2 và sercurity group.
neutron-dhcp-agent: Cung cấp dhcp services cho virtual network.
neutron-metadata-agent: được cung cấp thông tin về cấu hình, metadata để instace trích xuất (vd: instance credentials, flavor, keypair) qua địa chỉ 169.254.168.254
neutron-l3-agent: cấu hình routing và NAT service cho Virtual Network
Các thành phần cần cài đặt trên Compute Node:

neutron-linuxbridge-agent: Cấu hình kết nối layer2 và sercurity group.
Mô hình

https://github.com/truongvh96/Openstack/blob/main/Install-Openstack-Ussuri-Ubuntu18.04-3node/Network/provider%26selfservice/network2-connectivity.png

Trên Controller Node (Network Node):

01 Bridge Provider được tạo kết nối tới Interface 2 (NIC vật lý) và Virtual Router
01 Bridge Self Service được tạo gồm 03 Interface ( 01 kết nối đến Virtual Router và 01 cho DHCP Server, 01 cho VXLAN Tunnel dùng chung với đường managerment tạo kết nối tới Compute Node.
01 DHCP Network Namespace được kết nối toi Bridge của mạng Self Service phục vụ cho việc cấp IP cho Instance.
01 Router Network Namespace (Virtual Router) có 2 interface cho provider network và self service (Provider có thể coi là dường Wan, Self Service là đường Lan)
Trên Compute Node :

01 Bridge Self Service gồm 01 interface Tap kết nối tới instance, 01 interface VXLAN Tunnel dùng chung với đường managerment tạo kết nối tới Network Node.
