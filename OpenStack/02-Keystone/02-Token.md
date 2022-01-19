### Lịch sử Token
- UUID Token
Vào những ngày đầu, Keystone hỗ trợ UUID token. Đây là loại token gồm 32 kí tự được dùng để xác thực và ủy quyền. Lợi ích mà loại token này mạng lại đó là nó nhỏ gọn và dễ sử dụng, có thể thêm trực tiếp vào câu lệnh curl. Tuy nhiên nó lại không thể chứa đủ thông tin thực hiện việc ủy quyền. Các service của OpenStack sẽ luôn phải gửi lại token này cho Keystone để xác thực xem hành động có hợp lệ không. Điều này khiến cho Keystone trở thành trung tâm cho mọi hoạt động của OpenStack
- PKI Token
Trong nỗ lực tìm kiếm một loại token mới để khắc phục những nhược điểm của UUID Token, nhóm phát triển đã tạo ra PKI Token. Token này chứa đủ thông tin để xác thực và ủy quyền và đồng thời nó cũng chứa cả danh mục dịch vụ. Bên cạnh đó, token này được gán vào và các dịch vụ có thể cache lại nó để sử dụng cho đến khi nó hết hiệu lực hoặc bị hủy. Loại token này vì thế cũng khiến lưu lượng traffic tới Keystone server ít hơn. Tuy nhiên kích cỡ của nó có thể lên tới 8k và điều này làm việc gán vào HTTP header trở nên khó khăn hơn. Rất nhiều các web server mặc định sẽ không cho phép điều này nếu chưa được config lại. Thêm vào đó, loại token này cũng rất khó được sử dụng trong câu lệnh curl .
- PKIz Token
Vì những hạn chế của PKI, các nhà phát triển đã cố gắng sửa đổi và ra mắt một phiên bản khác đó là PKIz, tuy nhiên theo đánh giá của cộng đồng thì loại token này vẫn rất lớn về kích thước.
- Fernet Token
Với tất cả các hạn chế trên, buộc Keystone team phải đưa ra một loại mới là Fernet Token. Fernet Token khá nhỏ (255 ký tự) tuy nhiên nó lại chưa đủ thông tin để ủy quyền. Bên cạnh đó, việc nó chứa đủ thông tin cũng không khiến token database phải lưu dữ liệu token nữa. Các nhà vận hành thường phải dọn dẹp Keystone token database để hệ thống của họ hoạt động ổn định. Mặc dù vậy, Fernet token có nhược điểm đó là symmetric keys được dùng để tạo ra token cần được phân phối và xoay vòng. Các nhà vận hành cần phải giải quyết vấn đề này, tuy nhiên họ có vẻ thích thú với việc này hơn là sử dụng những loại token khác.

### Tìm hiểu về Fernet Token
Fernet là token mặc định trên Keystone. Sử dụng 255 b
