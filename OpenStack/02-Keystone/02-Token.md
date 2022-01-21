### Lịch sử Token
- **UUID Token**
Vào những ngày đầu, Keystone hỗ trợ UUID token. Đây là loại token gồm 32 kí tự được dùng để xác thực và ủy quyền. Lợi ích mà loại token này mạng lại đó là nó nhỏ gọn và dễ sử dụng, có thể thêm trực tiếp vào câu lệnh curl. Tuy nhiên nó lại không thể chứa đủ thông tin thực hiện việc ủy quyền. Các service của OpenStack sẽ luôn phải gửi lại token này cho Keystone để xác thực xem hành động có hợp lệ không. Điều này khiến cho Keystone trở thành trung tâm cho mọi hoạt động của OpenStack
- **PKI Token**
Trong nỗ lực tìm kiếm một loại token mới để khắc phục những nhược điểm của UUID Token, nhóm phát triển đã tạo ra PKI Token. Token này chứa đủ thông tin để xác thực và ủy quyền và đồng thời nó cũng chứa cả danh mục dịch vụ. Bên cạnh đó, token này được gán vào và các dịch vụ có thể cache lại nó để sử dụng cho đến khi nó hết hiệu lực hoặc bị hủy. Loại token này vì thế cũng khiến lưu lượng traffic tới Keystone server ít hơn. Tuy nhiên kích cỡ của nó có thể lên tới 8k và điều này làm việc gán vào HTTP header trở nên khó khăn hơn. Rất nhiều các web server mặc định sẽ không cho phép điều này nếu chưa được config lại. Thêm vào đó, loại token này cũng rất khó được sử dụng trong câu lệnh curl .
- **PKIz Token**
Vì những hạn chế của PKI, các nhà phát triển đã cố gắng sửa đổi và ra mắt một phiên bản khác đó là PKIz, tuy nhiên theo đánh giá của cộng đồng thì loại token này vẫn rất lớn về kích thước.
- **Fernet Token**
Với tất cả các hạn chế trên, buộc Keystone team phải đưa ra một loại mới là Fernet Token. Fernet Token khá nhỏ (255 ký tự) tuy nhiên nó lại chưa đủ thông tin để ủy quyền. Bên cạnh đó, việc nó chứa đủ thông tin cũng không khiến token database phải lưu dữ liệu token nữa. Các nhà vận hành thường phải dọn dẹp Keystone token database để hệ thống của họ hoạt động ổn định. Mặc dù vậy, Fernet token có nhược điểm đó là symmetric keys được dùng để tạo ra token cần được phân phối và xoay vòng. Các nhà vận hành cần phải giải quyết vấn đề này, tuy nhiên họ có vẻ thích thú với việc này hơn là sử dụng những loại token khác.

### Tìm hiểu về Fernet Token

- Là loại token mới nhất, được tạo ra để khắc phục những hạn chế của các loại token trước đó. Thứ nhất, nó khá nhỏ với khoảng 255 ký tự, lớn hơn UUID nhưng nhỏ hơn rất nhiều với PKI. Token này cũng chứa vừa đủ thông tin để cho phép nó không cần phải được lưu trên database .

- Fernet token chứa một lượng nhỏ dữ liệu ví dụ như thông tin để nhận diện người dùng, project, thời gian hết hiệu lực,... Nó được sign bởi symmetric key để ngăn ngừa việc giả mạo. Cơ chế hoạt động của loại token này giống với UUID vì thế nó cũng phải được validate bởi Keystone .

- Một trong những vấn đề của loại token này đó là nó dùng symmetric key để mã hóa token và các key này buộc phải được phân phối trên tất cả các region của OpenStack. Thêm vào đó, key cũng cần được rotated.

**Đặc điểm :**

- Sử dụng mã hóa bất đối xứng (sử dụng chung key đẻ mã hóa và giải mã)
- Có kích thước khoảng 255 bytes, không nén, lớn hơn UUID và nhỏ hơn PKI
- Chứa các thông tin cần thiết như userid, projectid, domainid, methods, expiresat,... Không chứa service catalog
- Không lưu token vào database
- Cần phải gửi lại Keystone để xác nhận, tương tự UUID
- Cần phải phân phối khóa cho các khu vực cách nhau trong OpenStack
- Sử dụng cơ chế xoay khóa để tăng tính bảo mật
- Nhanh hơn 85% so với UUID và 89% so với PKI
- Fernet Keys lưu trưx trong thư mục /etc/keystone/fernet-keys
- Mã hóa với Primary Fernet Key
- Giải mã với danh sách các Fernet Key

**Ưu điểm :**

- Nhẹ hơn PKI và PKIz
- Không cần lưu trữ trong DB
- Trong token mang thông tin
- Hỗ trợ Multi OpenStack

**Nhược điểm :**

Quá trình xác thực tăng hoạt động thu hồi
VD về 1 đoạn Fernet token :
```
gAAAAABU7roWGiCuOvgFcckec-0ytpGnMZDBLG9hA7Hr9qfvdZDHjsak39YN98HXxoYLIqVm19Egku5YR
3wyI7heVrOmPNEtmr-fIM1rtahudEdEAPM4HCiMrBmiA1Lw6SU8jc2rPLC7FK7nBCia_BGhG17NVHuQu0
S7waA306jyKNhHwUnpsBQ%3D
```
Các file key trong thư mục /etc/keystone/fernet-keys :
0 1 2 3 4

##### Các loại Fernet key :

Loại 1 : Primary key
- Dùng để mã hóa và giải mã
- Filename có số index cao nhất
Loại 2 : Secondary key
- Chỉ được dùng để giải mã
- Filename có số index nằm giữa Primary key và Staged key
Loại 3: Staged key
- Giải mã và chuẩn bị để chuyển thành primary key
- Filename có số index nhỏ nhất (0)

##### Quá trình Fernet Key rotation

![image](https://user-images.githubusercontent.com/97424062/150483638-9943f363-2abb-4720-a439-da447376ab8e.png)

VD : Giả sử triển khai hệ thống cloud với Keystone ở 2 bên W và E. Cả 2 repo này đều được thiết lập với 3 fernet key như sau :
ls /etc/keystone/fernet-keys
0 1 2
Ở đây 2 sẽ trở thành primary key để mã hóa fernet token. Khi Keystone bên W nhận token từ E (mã hóa bằng key 2), W sẽ xác thực token này, giải mã bằng 4 key theo thứ tự 3, 2, 1, 0. Keystone bên E nhận fernet token từ W (mã hóa bằng key 3), E xác thực token này vì key 3 bên W lúc này trở thành staged key (0) bên E, keystone E giải mã token với 3 key theo thứ tự 2, 1, 0. Có thể cấu hình giá trị max_active_keys trong file /etc/keystone.conf để quy định tối đa số key tồn tại trong keystone. Nếu số key vượt giá trị này thì key cũ sẽ bị xóa.
Kế hoạch cho vấn đề rotated keys :
Khi sử dụng fernet tokens yêu cầu chú ý về thời hạn của token và vòng đời của key. Vấn đề nảy sinh khi secondary keys bị remove khỏi key repos trong khi vẫn cần dùng key đó để giải mã một token chưa hết hạn (token này được mã hóa bởi key đã bị remove). Để giải quyết vấn đề này, trước hết cần lên kế hoạch xoay khóa. Ví dụ bạn muốn token hợp lệ trong vòng 24 giờ và muốn xoay khóa cứ mỗi 6 giờ. Như vậy để giữ 1 key tồn tại trong 24h cho mục đích decrypt thì cần thiết lập max_active_keys=6 trong file keytone.conf (do tính thêm 2 key đặc biệt là primary key và staged key). Điều này giúp cho việc giữ tất cả các key cần thiết nhằm mục đích xác thực token mà vẫn giới hạn được số lượng key trong key repos (/etc/keystone/fernet-keys/).
token_expiration = 24
rotation_frequency = 6
max_active_keys = (token_expiration / rotation_frequency) + 2

##### Các trường của Fernet Token
```
Version ‖ Timestamp ‖ IV ‖ Ciphertext ‖ HMAC
```
Trong đó :
Version: Fernet Format Version (0x80) - 8 bits, biểu thị phiên bản của định dạng token
Timestamp: số nguyên 64-bit không dấu, chỉ nhãn thời gian tính theo giây, tính từ 1/1/1970, chỉ ra thời điểm token được tạo ra.
IV (Initialization Vector): key 128 bits sử dụng mã hóa AES và giải mã Ciphertext
Ciphertext: là keystone payload kích thước biến đổi tùy vào phạm vi của token. Cụ thể hơn, với token có phạm vi project, Keystone Payload bao gồm: version, user id, method, project id, expiration time, audit ids
HMAC: 256-bit SHA256 HMAC (Keyd-Hash Messasge Authentication Code) - Mã xác thực thông báo sử dụng hàm một chiều có khóa với signing key kết nối 4 trường ở trên.

##### Quá trình lấy token (Token Generation Workflow)

![image](https://user-images.githubusercontent.com/97424062/150483461-8135beef-119a-4342-ba18-74669a4a4a9f.png)

Với key và message nhận được, quá trình tạo fernet token như sau:
Ghi thời gian hiện tại vào trường timestamp
Lựa chọn một IV duy nhất
Xây dựng ciphertext:
Padd message với bội số là 16 bytes (thao tác bổ sung một số bit cho văn bản trong mã hóa khối AES)
Mã hóa padded message sử dụng thuật toán AES 128 trong chế độ CBC với IV đã chọn và encryption-key được cung cấp
Tính toán trường HMAC theo mô tả trên sử dụng signing-key mà người dùng được cung cấp
Kết nối các trường theo đúng format token ở trên
Mã hóa base64 toàn bộ token

##### Quá trình validate token (Token validation workflow)
![image](https://user-images.githubusercontent.com/97424062/150483378-b1fbb87c-6604-4776-99bc-c2433bd90fce.png)

B1 : Gửi yêu cầu xác thực token với API GET v3/auth/tokens
B2 : Khôi phục lại padding, trả lại token với padding chính xác
B3 : Decrypt sử dụng Fernet Keys để thu lại token payload
B4 : Xác định phiên bản của token payload. (Unscoped token: 0, Domain scoped payload: 1, Project scoped payload: 2)
B5 : Tách các trường của payload để chứng thực. Ví dụ với token trong tầm vực project gồm các trường sau: user id, project id, method, expiry, audit id
B6 : Kiểm tra xem token đã hết hạn chưa. Nếu thời điểm hiện tại lớn hơn so với thời điểm hết hạn thì trả về thông báo "Token not found". Nếu token chưa hết hạn thì chuyển sang bước tiếp theo
B7 : Kiểm tra xem token đã bị thu hồi chưa. Nếu token đã bị thu hồi (tương ứng với 1 sự kiện thu hồi trong bảng revocation_event của database keystone) thì trả về thông báo "Token not found". Nếu chưa bị thu hồi thì trả lại token (thông điệp phản hồi thành công HTTP/1.1 200 OK)

- Multiple Data Centers

Vì Fernet key không cần phải được lưu vào database nên nó có thể hỗ trợ multiple data center. Tuy nhiên keys sẽ phải được phân phối tới tất cả các Region

### Horizon và token
#### Cách mà Horizon dùng token :
- Tokens được sử dụng cho mỗi lần login của user
Horizon lấy unscoped token cho user và sau dựa vào các request để cung cấp các project scoped token
Token có thể được tái sử dụng bằng cách lưu lại sau mỗi session
- Các method để lưu lại token:
Local memory cache
Cookie Backend
Memcached
Database
Cached Database

- Cookie backend

Là phương thức mặc định của devstack
Token được lưu trên cookie của trình duyệt
Có khả năng co giãn cao
Khi cookie đầy, dễ dẫn tới tình trạng không xác thực được user -> back to login

- Memcache backend

Cho phép lưu một lượng lớn token
Token được lưu ở phía server
Yêu cầu cấu hình memcached
Có thể sử dụng với backing DB
