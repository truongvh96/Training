#### 1. 
Linux Kernel là phần core của hệ điều hành, cho phép quản lý, điều khiển, giao tiếp với các phần cứng của máy tính. Kernel có chứa phần mềm cho phép người dùng sử dụng được ổ cứng, mạng, RAM hay các phần cứng khác. Hệ thống Linux dựa theo UNIX, gọi là GNU/Linux. Nhân Linux hiện nay được phát triển bởi cộng đồng nguồn mở dựa trên nhân Linux mới được phát triển bởi Linus Torvalds.

#### 2.
Distro Linux là gì ?Các bản phân phối Linux hay còn gọi là Distro Là tập hợp các ứng dụng, package, trình quản lý gói và các tính năng chạy trên Linux Kernel. Linux Kernel dùng chung cho tất cả các Distro. Đôi khi với các bản phân phối nhất định Linux Kernel sẽ được customize lại theo mục đích riêng.

#### 3. Chọn bản phân phối Linux
Sử dụng Linux bạn có thể nghe thấy rất nhiều bản phân phối Linux khác nhau như Ubuntu, Debian, Fedora, Red Hat … (Xem danh sách hàng trăm Distro phổ biến nhất gần đây tại distrowatch) Điều này làm cho việc mới tiếp cận Linux có vẻ lúng túng không biết chọn bản phân phối nào. Mỗi bản phân phối có những đặc tính khác nhau, có thể phân chia để lựa chọn theo ba tiêu chí:

#### Mục đích:
- Cấu hình và gói ứng dụng
Mô hình hỗ trợ Bản phân phối linux theo mục đích sử dụng: Mỗi distro được thiết kế cho các mục đích khác nhau, cung cấp trải nghiệm người dùng khác nhau. Một số distro dùng để làm server, một số lại dùng cho môi trường desktop, một số lại có mục đích đặc biệt như hệ thống nhúng. Bản phân phối linux theo cách cấu hình: Các bản phân phối còn có sự khác nhau chính đó là cách thức thiết lập cấu hình hệ thống của chúng khác nhau. Một số Distro giữ các cấu hình, thiết lập, các file cấu hình ở cùng một nơi (thư mục), một số khác lại lưu ở nhiều nơi trong cấu trúc thư mục. Tiếp theo là quá trình cài đặt, cập nhật các ứng dụng (các gói package) cũng khác nhau tùy vào distro, nhiều distro thực hiện điều này bằng các công cụ quản lý gói (package) như DPKG (debian), APT (ubuntu, debian), RPM (Red Hat), YUM … Do có nhiều trình quản lý gói kiểu này nên thật sự việc quản trị gây khó khi làm việc giữa nhiều distro, ở các bài sau sẽ nói về những công công cụ quản lý gói này. Bản phân phối linux theo mô hình hỗ trợ: Một số distro được bảo trì, hỗ trợ, đóng góp bởi cộng đồng (Debian, CentOS, Fedora) nhưng cũng có các distro được hỗ trợ bởi các công ty thương mại (RHEL, Ubuntu), dù phần mềm vẫn là nguồn mở nhưng bạn cần trả tiền cho các dịch vụ hỗ trợ.
#### 3. Các Distro Linux phổ biến nhất
##### 3.1. Red Hat
- RHEL (Red Hat Enterprise Linux): Đây là phân phối được phát triển bởi Red Hat với mục đích thương mại, khi mua license người dùng sẽ được support và nhận các bản vá, update, gói cài đặt ổn định nhất.
- CentOS: đây là distro dựa theo RHEL, được cung cấp miễn phí. Các package cũng được quản lý với RPM.
- Fedora: là bản phân phối có sự tham gia của cộng đồng và Red Hat, nó dựa theo RHEL và cung cấp nền tảng phát triển tốt. Do được tài trợ bở Red Hat, Fedora được dùng như bản test các tính năng mới của Red Hat trước khi tính năng đó đưa vào bản thương mại của RHEL, Fedora cũng dùng trình quản lý gói RPM và các công cụ quản trị giống Red Hat.
##### 3.2. Debian
- Debian Linux: là phiển bản miễn phí, phát triển và phân phổi bởi cộng đồng đông đảo các lập trình viên và người dùng. Debian là tự do, nguồn mở và duy trì dựa trên những yêu cầu mà người dùng mong muốn. Quản lý gói trên Debian là dpkg
- Ubuntu: Đây là bản phân phối miễn phí dựa trên Debian với vòng đời phát triển, cập nhật cứ 6 tháng một. Nó cũng có hỗ trợ thương mại dành cho các tổ chức. Ubuntu được sử dụng với với nhiều mục đích khác nhau gồm cả desktop và server. Nhiều người nhận định Ubuntu là một phân phối Linux dễ hiểu, dễ sử dụng, mang lại trải nghiệm người dùng tốt nhất. Nó cũng sử dụng trình quản lý gói giống Debian và các công cụ quản trị của nó.
##### 3.3. Slackware
- Gentoo: là phiên bản cộng đồng – miễn phí, cung cấp các tùy chọn để biên dịch ra bản Linux tùy thuộc vào phần cứng của bạn, nó không cung cấp các gói ứng dụng được biên dịch sẵn mà hầu hết người dùng sẽ tự biên dịch từ mã nguồn.

Trong các Distro trên nếu dùng ở môi trường Desktop nên chọn Ubuntu, nếu dùng làm Server có thể chọn CentOS, Ubuntu Server. Với doanh nghiệp lớn hoặc các dn hoạt động với các lĩnh vực tài chính, ngân hàng nên sử RHEL làm Server với tính ổn định, các bản vá lỗi, lỗ hổng kịp thời và nhận được Support từ hãng.
