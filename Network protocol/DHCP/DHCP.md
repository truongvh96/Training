#### DHCP là gì ?

- DHCP (Dynamic Host Configuration Protocol): đây là giao thức cấu hình host động. cung cấp IP, netmask, default gateway. Điều này thường được cấp phát bởi DHCP Server được tích hợp sẵn trên router hoặc modem nhà mạng.

- DHCP sử dụng port UDP (67 & 68). Port 67 (ingoing) listen từ client và port 68 (outgoing) để response tới client.

##### DHCP Nguyên lý hoạt động và thành phần :

Các thành phần chính trong DHCP:

- DHCP Server : Chứa địa chỉ IP và các cấu hình liên quan
- DHCP Client: End User nhận thông tin cấu hình từ DHCP Server.
- IP Address Pool: Pham vị IP có sẵn cho DHCP Client,
- Subnet : Mặt nạ mạng con mà DHCP cung cấp.
- Lease : Khoảng thời gian mà Client cần renew IP.
- DHCP Relay: Máy chủ lắng nghe các bản tin từ Client và tương tác tới DHCP Server. Đồng thời phản hồi bản tin tới Client. DHCP Relay có thể được tích hợp chung trên DHCP Server.

**Các bản tin trong DHCP gồm 4 bản tin:**

- Discovery
- Offer
- Request
- Ack

Quá trình client xin cấp DHCP từ server.

- B1 DHCP Discovery Client sẽ gửi thông điệp broadcast trên subnet để tìm server DHCP khả dụng. Client tạo ra một gói tin UDP (User Datagram Protocol) với đích đến mặc định 255.255.255.255 hoặc địa chỉ broadcast subnet cụ thể nếu được cấu hình.

- B2 DHCP offer Khi DHCP server nhận được truy vấn cần cấp phát IP từ một client, nó sẽ bảo lưu địa chỉ IP cho client và mở rộng địa chỉ IP sẽ cấp phát bằng cách gửi cho client message DHCPOFFER. Thông điệp này chứa địa chỉ MAC của client, địa chỉ IP mà server sẽ cung cấp, subnet mask, thời gian được cấp phát và địa chỉ IP của DHCP server cung cấp.

- B3 DHCP request Client trả lời DHCP request, nó sẽ gửi tin unicast tới server mà thông tin địa chỉ nằm trong DHCP offser mà nó nhận được. Dựa trên trường Transaction ID trong request, khi đó servers sẽ nhận được thông báo những offer mà client chấp nhận. Khi các DHCP server khác nhận thông điệp này, chúng sẽ loạt bỏ bất kỳ offer nào nó đã gửi tới client và lấy lại các địa chỉ IP offer này đưa vào danh sách các địa chỉ IP có sẵn

- B4 DHCP acknowledgement Khi DHCP server nhận được thông điệp DHCPREQUEST từ Client, quá trình bước vào giai đoạn cuôí cùng. Giai đoạn chấp nhận liên quan đến việc gửi một gói DHCPACK tới Client. Gói tin này bao gồm thời gian được sử dụng và thông tin cấu hình khác mà client có thể đã truy vấn. Tại điểm này, quá trình cấu hình IP đã được hoàn thành.
