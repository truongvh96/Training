1. Khái niệm ARP
- ARP ( Address resolution prrotocol ) là protocol dùng để kết nối IP Address tới Mac Address, hoạt động ở tầng Network trong mồ hình OSI. Nó dịch địa chỉ IP 32bit thành địa chỉ MAC 48bit
2. ARP hoạt động thế nào?
- Khi một ccomputer join vào mạng LAN sẽ được cấp một địa chỉ IP duy nhất để định danh trong mạng và giao tiếp với các thiết bị khác.
- Khi device cần biết MAC address của device khác nó gửi một gói tin quảng bá (broadcast) ARP Request bao gồm MAC Address và IP của device cần request MAC address.
- Tất cả device trên toàn mạng nhận được gói tin quảng bá và so sánh với IP của chính nó nếu match nó sẽ response cho device request một packet gồm MAC Address.
3. Package Structure
3.1.Cấu trúc của ARP packet

Gồm có 4 thành phần

- Ethernet header
- ARP Message
- Padding
- Ethernet CRC

3.2.Cấu trúc của ARP message

Các thành phần có trong ARP Message

- Hardware type (HTYPE) : Trường này cho biết network protocol . Example : Ethernet là “1” .
- Protocol type ( PTYPE ) : Trường này cho biết internetwork protocol . Example : IPv4 là “0x0800”
- Hardware length ( HLEN ) : Độ dài ( octets) của hardware address . Ethernet address có độ dài là 6 octets .
- Protocol length ( PLEN ) : Độ dài của internetwork address được sử dụng . IPv4 address có độ dài là 4 octets .
- Operation : Cho biết packet là “ARP request” hay “ARP reply” . “ARP request” là “1” và “ARP reply” là “2” .
- Sender hardware address (SHA) : MAC address của sender .
- Sender protocol address (SPA) : Internetwork address (IP) của sender .
- Target hardware address (THA) : MAC address của receiver .
- Target protocol address (TPA) : Internetwork address (IP) của receiver
