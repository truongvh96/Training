## **1) Giới thiệu**
- Môi trường cloud theo mô hình dịch vụ **Infrastructure-as-a-Service** layer (**IaaS**) cung cấp cho người dùng khả năng truy cập vào các tài nguyên quan trọng ví dụ như máy ảo, kho lưu trữ và kết nối mạng. Bảo mật vẫn luôn là vấn đề quan trọng đối với mọi môi trường cloud và trong **OpenStack** thì **Keystone** chính là dịch vụ đảm nhiệm công việc ấy. Nó sẽ chịu trách nhiệm cung cấp các kết nối mang tính bảo mật cao tới các nguồn tài nguyên cloud.
## **2) Các chức năng chính của Keystone**
### **2.1) Identity *(nhận diện)***
- Nhận diện những người đang cố truy cập vào các tài nguyên trên Cloud
- Trong **Keystone**, **identity** thường được hiểu là User
Tại những mô hình OpenStack nhỏ, identity của user thường được lưu trữ trong database của keystone. Đối với những mô hình lớn cho doanh nghiệp thì 1 external Identity Provider thường được sử dụng.
### **2.2) Authentication *(xác thực)***
- Là quá trình xác thực những thông tin dùng để nhận định user (***user's identity***)
- **Keystone** có tính pluggable tức là nó có thể liên kết với những dịch vụ xác thực người dùng khác như **LDAP** hoặc **Active Directory**
- Thường thì **Keystone** sử dụng Password cho việc xác thực người dùng. Đối với những phần còn lại, **keystone** sử dụng tokens.
- **OpenStack** dựa rất nhiều vào tokens để xác thực và **Keystone** chính là dịch vụ duy nhất có thể tạo ra tokens
- Token có giới hạn về thời gian được phép sử dụng. Khi token hết hạn thì user sẽ được cấp một token mới. Cơ chế này làm giảm nguy cơ user bị đánh cắp token.
- Hiện tại, **Keystone** đang sử dụng cơ chế ***bearer token***. Có nghĩa là bất cứ ai có token thì sẽ có khả năng truy cập vào tài nguyên của cloud. Vì vậy việc giữ bí mật token rất quan trọng.
    > ***Bearer token*** là một giá trị nằm trong phần Authorization header của mỗi HTTP request. Nó mặc định không tự được lưu ở bất cứ đâu (không như **cookie**), bạn phải quyết định nơi lưu nó.
### **2.3) Access Management - Authorization *(Quản lý truy cập, quyền hạn)***
- **Access Management** hay còn được gọi là **Authorization** là quá trình xác định những tài nguyên mà user được phép truy cập tới.
- Trong **OpenStack**, **Keystone** kết nối users với những Projects hoặc Domains bằng cách gán role cho user vào những project hoặc domain ấy.
- Các projects trong **OpenStack** như **Nova**, **Cinder**...sẽ kiểm tra mối quan hệ giữa role và các user's project và xác định giá trị của những thông tin này theo cơ chế các quy định (**policy engine**). **Policy engine** sẽ tự động kiểm tra các thông tin (thường là **role**) và xác định xem user được phép thực hiện những gì.
## **3) Lợi ích khi sử dụng Keystone**
- Cung cấp giao diện xác thực và quản lí truy cập cho các services của **OpenStack**. Nó cũng đồng thời lo toàn bộ việc giao tiếp và làm việc với các hệ thống xác thực bên ngoài.
- Cung cấp danh sách đăng kí các containers (Projects) mà nhờ vậy các services khác của **OpenStack** có thể dùng nó để "tách" tài nguyên (servers, images,...)
- Cung cấp danh sách đăng kí các Domains được dùng để định nghĩa các khu vực riêng biệt cho users, groups, và projects khiến các khách hàng trở nên "tách biệt" với nhau
- Danh sách đăng kí các roles được **Keystone** dùng để ủy quyền cho các services của **OpenStack** thông qua file `policy`.
- **Assignment store** cho phép users và groups được gán các roles trong projects và domains.
- **Catalog** lưu trữ các services của **OpenStack**, **endpoints** và **regions**, cho phép người dùng tìm kiếm các endpoints của services mà họ cần.
## **4) Các khái niệm lớn trong Keystone**
### **4.1) Project**
- Trong **Keystone**, **Project** được dùng bởi các service của **OPS** để nhóm và cô lập các nguồn tài nguyên. Nó có thể hiểu là 1 nhóm các tài nguyên mà chỉ có một số các user mới có thể truy cập và hoàn toàn tách biệt với các nhóm khác.
- Ban đầu nó được gọi là **tenants** sau đó được đổi tên thành **projects**.
- Mục đích cơ bản nhất của **Keystone** chính là nơi để đăng ký cho các **projects** và xác định ai được phép truy cập project đó.
- Bản thân **projects** không sở hữu **users** hay **groups** mà **users** và **groups** được cấp quyền truy cập tới **project** sử dụng cơ chế gán **role**.
- Trong một vài tài liệu của **OpenStack** thì việc gán **role** cho **user** còn được gọi là "`grant`".

    <img src=https://i.imgur.com/wufMuvX.png>

### **4.2) Domains**
- Trong thời kì đầu, không có bất cứ cơ chế nào để hạn chế sự xuất hiện của các **project** tới những nhóm user khác nhau. Điều này có thể gây ra những sự nhầm lẫn hay xung đột không đáng có giữa các tên của **project** của các tổ chức khác nhau.
- Tên **user** cũng vậy và nó hoàn toàn cũng có thể dẫn tới sự nhầm lẫn nếu hai tổ chức có **user** có tên giống nhau.
- Vì vậy mà khái niệm **Domain** ra đời, nó được dùng để cô lập danh sách các **Projects** và **Users**.
- **Domain** được định nghĩa là một tập hợp các **users**, **groups**, và **projects**. Nó cho phép người dùng chia nguồn tài nguyên cho từng tổ chức sử dụng mà không phải lo xung đột hay nhầm lẫn.

    <img src=https://i.imgur.com/nAhlHpT.png>

### **4.3) User và User Group (Actor)**
- Trong **Keystone**, **user** và **user groups** là những đối tượng được cấp phép truy cập tới các nguồn tài nguyên được cô lập trong **domains** và **project**.
- **Groups** là một tập hợp các **user** .
- **User** và **user groups** gọi là các **actor**
- Mối quan hệ giữa **domains**, **project**, **users** và **groups** thể hiện qua hình sau :

    <img src=https://i.imgur.com/vZiGnTu.png>

### **4.4) Roles**
- **Roles** được dùng để hiện thực hóa việc cấp quyền trong **Keystone** .
- Mỗi **actor** có thể có nhiều **roles** đối với từng **project** khác nhau .
- **Role** được gán cho **user** và trên một **project** cụ thể  (*"assigned to" user, "assigned on" project*)
### **4.5) Assignment**
- **Role assignment** gồm một **Role**, một **Resource** và một **Identity** .
- **Role assignment** được cấp phát, thu hồi, và có thể được kế thừa giữa các **users**, **groups**, **project** và **domains**.
### **4.6) Target**
- **Project** và **domain** đều có thể gán **role**. Từ đó sinh ra khái niệm **target** .
- **Target** chính là **project** hoặc **domain** nào sẽ được gán **role** cho **user** .
### **4.7) Token**
- Để user truy cập bất cứ **OpenStack API** nào thì user cần chúng minh họ là ai và họ được phép gọi tới **API**. Để làm được điều này, họ cần có **token** và "dán" chúng vào "API call". **Keystone** chính là service chịu trách nhiệm tạo ra **tokens**.
- Sau khi được xác thực thành công bởi **keystone** thì user sẽ nhận được **token**. **Token** cũng chứa các thông tin ủy quyền của **user** trên cloud.
- **Token** có chứa cả phần **ID** và **payload**. **ID** được dùng để đảm bảo rằng **token** là duy nhất trên cloud và **payload** chứa thông tin của **user** .
### **4.8) Catalog**
- Chứa **URLs** và **endpoints** của các services khác nhau.
- Nếu không có **Catalog**, **users** và các ứng dụng sẽ không thể biết được nơi cần chuyển yêu cầu để tạo máy ảo hoặc lưu dữ liệu.
- Service này được chia nhỏ thành danh sách các **endpoints** và mỗi một **endpoint** sẽ chứa ***admin URL***, ***internal URL***, and ***public URL***.
## **5) Chi tiết các chức năng của Keystone**
### **5.1) Identity**
- **Identity service** trong keystone cung cấp các **Actors**. Nó có thể tới từ nhiều dịch vụ backend khác nhau như **SQL**, **LDAP**, và **Federated Identity Providers**.
#### **5.1.1) SQL Backend**
- **Keystone** có tùy chọn cho phép lưu trữ **actors** trong **SQL**. Nó hỗ trợ các database như **MySQL**, **MariaDB**, **PostgreSQL**, và **DB2** .
- **Keystone** sẽ lưu thông tin như tên, mật khẩu và mô tả.
- Những cài đặt của các database này nằm trong file config của **Keystone** .
- Về bản chất, **Keystone** sẽ hoạt động như 1 **Identity Provider** (*nhà cung cấp chứng thực*). Vì thế đây sẽ không phải là lựa chọn tốt nhất trong một vài trường hợp, nhất là đối với các khách hàng là doanh nghiệp.
- Ưu điểm :
    - Dễ setup
    - Quản lý **users**, **groups** thông quan **OpenStack APIs**
#### **5.1.2) LDAP Backend**
- Keystone cũng có tùy chọn cho phép lưu trữ **actors** trong **Lightweight Directory Access Protocol (LDAP)**.
- **Keystone** sẽ truy cập **LDAP** như các ứng dụng khác (***System Login***, ***Email***, ***Web Application***, ...)
- Thiết lập kết nối sẽ được lưu trong file config của **Keystone**. Các cài đặt bao gồm tùy chọn cho phép **Keystone** được ghi hoặc chỉ đọc dữ liệu từ **LDAP**.
- Thông thường **LDAP** chỉ cho phép các câu lệnh đọc (ví dụ như tìm kiếm **user**, **group** và xác thực)
- Nếu sử dụng **LDAP** như một read-only Identity Backends thì **Keystone** cần có quyền truy cập **LDAP**
- Ưu điểm :
    - Không cần sao lưu tài khoản người dùng
    - **Keystone** sẽ không hoạt động như một **Identity Provider** nữa .
- Nhược điểm :
    - Tài khoản cho các dịch vụ bị lưu không tập trung
    - **Keystone** có thể thấy mật khẩu người dùng khi chứng thực
#### **5.1.3) Multiple Backends**
- Kể từ bản ***Juno*** thì **Keystone** đã hỗ trợ nhiều Identity backends cho V3 Identity API. Nhờ vậy mà mỗi một domain có thể có một identity source (backend) khác nhau.
- **Domain** mặc định thường sử dụng **SQL backend** bởi nó được dùng để lưu các host **service accounts**. **Service accounts** là các tài khoản được dùng bởi các dịch vụ **OpenStack** khác nhau để tương tác với **Keystone**.
- Việc sử dụng **Multiple Backends** được lấy cảm hứng trong các môi trường doanh nghiệp, **LDAP** chỉ được sử dụng để lưu thông tin của các nhân viên bởi **LDAP admin** có thể không ở cùng một công ty với nhóm triển khai **OpenStack**. Bên cạnh đó, nhiều **LDAP** cũng có thể được sử dụng trong trường hợp công ty có nhiều phòng ban.
- Ưu điểm :
    - Cho phép việc sử dụng nhiều hệ thống **LDAP** để lưu tài khoản người dùng và **SQL** để lưu tài khoản dịch vụ
    - Sử dụng lại các hệ thống **LDAP** đang có
- Nhược điểm :
    - Phức tạp trong khâu setup
    - Xác thực tài khoản phải dùng trong miền scoped
#### **5.1.4) Identity Providers Backend**
- Kể từ bản ***Icehouse*** thì **Keystone** đã có thể sử dụng các liên kết xác thực thông qua module **Apache** cho các **Identity Providers** khác nhau.
- Cơ bản thì **Keystone** sẽ sử dụng một bên thứ 3 để xác thực, nó có thể là những phần mềm sử dụng các backends (**LDAP**, **AD**, **MongoDB**) hoặc mạng xã hội (**Google**, **Facebook**, **Twitter**).
- Ưu điểm :
    - Có thể tận dụng phần mềm và cơ sở hạ tầng cũ để xác thực cũng như lấy thông tin của users.
    - Tách biệt **keystone** và nhiệm vụ định danh, xác thực thông tin.
    - Mở ra cánh cửa mới cho những khả năng mới ví dụ như ***single sign-on*** (Đăng nhập 1 lần theo phiên - **SSO**) và **hybrid cloud**
    - **Keystone** không thể xem mật khẩu, mọi thứ đều không còn liên quan tới **Keystone**.
- Nhược điểm :
    - Là loại backend setup phức tạp nhất
#### **5.1.5) Một số usecase trong thực tế**

| Identity Source | Usecases |
|-----------------|----------|
| **SQL Backend** | - Testing hoặc developing với **OpenStack**<br>- Ít users<br>- OpenStack-specific accounts (service users) |
| **LDAP Backend** | - Nếu có sẵn **LDAP** trong công ty<br>- Sử dụng một mình **LDAP** nếu bạn có thể tạo service accounts trong **LDAP** |
| **Multiple Backends** | - Được sử dụng nhiều trong các doanh nghiệp<br>- Dùng nếu service user không được cho phép trong **LDAP** |
| **Identity Provider Backend** | - Nếu bạn muốn có những lợi ích từ cơ chế mới ***Federated Identity***<br>- Nếu đã có sẵn **identity provider**<br>- **Keystone** không thể kết nối tới **LDAP**<br>- **Non-LDAP** identity source |

### **5.2) Authentication**
- Có rất nhiều cách để xác thực với **Keystone** service, trong đó 2 phương thức được dùng nhiều nhất là **password** và **token**.
#### **5.2.1) Password**
- Phương thức phổ biến nhất cho **user** hoặc service để xác thực đó là dùng **password**. Đoạn **payload** phía dưới là ví dụ cho `POST` request tới **Keystone**. Người dùng có thể nhận được những thông tin cần thiết cho việc xác thực.

    <img src=https://i.imgur.com/FKCEg2Q.png>

- **User section** nhận diện thông tin domain, **user’s globally unique ID** được sử dụng để tránh nhầm lẫn giữa các **user** trùng tên.
- **Scope section** là tùy chọn nhưng thường được sử dụng để **user** thu thập **service catalog**. **Scope** được sử dụng để xác định **project** nào **user** được làm việc. Nếu **user** không có **role** trên **project**, request sẽ bị loại bỏ. Tương tự như **user section**, **scope section** phải có đủ information về **project** để tìm nó, **domain** phải được chỉ định, bởi **project name** cũng có thể trùng nhau giữa các **domain** khác nhau. Trừ khi cung cấp **project ID**(duy nhất trên tất cả các **domain**), khi đó không cần thông tin **domain** nữa.
- **User** request **token** bằng `username`, `password` và `project scope`. **Token** sau đó sẽ được sử dụng ở các **OpenStack** service khác.

    <img src=https://i.imgur.com/QZGSuLu.png>

#### **5.2.2) Token**
- Giống như **password**, **user** có thể yếu cầu 1 **token** mới từ **token** hiện tại. Payload của `POST` request này sẽ ít code hơn của **password**.

    <img src=https://i.imgur.com/9QTyK8s.png>

    <img src=https://i.imgur.com/PavGeAl.png>

### **5.3) Access Management - Authorization**
- Quản lí truy cập và quy định **user** được sử dụng APIs nào là một trong những yếu tố quyết định khiến **Keystone** trở nên quan trọng trong **OpenStack**. Về bản chất, **Keystone** sẽ tạo ra **Role-Based Access Control (RBAC) policy** trên mỗi một public APIs endpoints. Các policies này nằm trên file `policy.json`.
- Dưới đây là một ví dụ về file `policy.json`, chứa **targets** và **rules**. **Targets** nằm bên trái và **rules** nằm ở phía bên phải. Trên đầu file, **targets** được thiết lập để xác định admin, owner và những user khác.

    <img src=https://i.imgur.com/Sl7I07r.png>

- Mỗi **rule** sẽ bắt đầu với `identity:` và xác định 1 controller có quyền quản lí API. Những **targets** đã được thiết lập được dùng để bảo vệ những **targets** mới. Bảng dưới đây mô tả việc mapping và bảo vệ giữa target và API.

    <img src=https://i.imgur.com/dugsfKZ.png>

## **6) Tóm tắt việc sử dụng các Backend cho Service**
- Hình dưới đây là bản tóm tắt cho các services và backends đi kèm.
    - Phần màu xanh lá cây thường sử dụng **SQL**.
    - Phần màu đỏ là backend thường sử dụng **LDAP** hoặc **SQL**,
    - Phần màu xanh da trời thường sử dụng **SQL** hoặc **Memcache**.
    - Còn policy service sẽ được lưu dưới dạng **file**.
- Ngoài ra **Keystone** còn có thêm các services khác tuy nhiên đây là những services được dùng phổ biến nhất.

    <img src=https://i.imgur.com/W597o8Z.png>
