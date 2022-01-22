## **1) Các trạng thái của image**
- `queued` : trạng thái của image được bảo vệ trong **glance registry**. Không có dữ liệu nào của image được tải lên **Glance** và kích thước của image không được thiết lập về zero khi khởi tạo.
- `saving` : biểu thị rằng dữ liệu của image đang được upload lên **glance**. Khi một image đăng ký với một call đến POST /image và có một x-image-meta-location header, image đó sẽ không bao giờ được trong tình trạng `saving` (dữ liệu Image đã có sẵn ở vị trí khác).
- `active` : biểu thị một image đó là hoàn toàn sẵn sàng trong **Glance**. Điều này xảy ra khi các dữ liệu image được tải lên.
- `deactivated` : Trạng thái biểu thị việc không được phép truy cập vào dữ liệu của image với tài khoản không phải admin. Khi image ở trạng thái này, ta không thể tải xuống cũng như export hay clone image.
- `killed` : trạng thái biểu thị rằng có vấn đề xảy ra trong quá trình tải dữ liệu của image lên và image đó không thể đọc được
- `deleted` : trạng thái này biểu thị việc **Glance** vẫn giữ thông tin về image nhưng nó không còn sẵn sàng để sử dụng nữa. Image ở trạng thái này sẽ tự động bị gỡ bỏ vào ngày hôm sau.
## **2) Luồng hoạt động của Glance**
- Qúa trình tạo image :
    - Tạo image, image sẽ được đưa vào hàng đợi và được nhận diện trong khoảng thời gian ngắn, được bảo vệ và sẵn sàng tải lên -> Lúc này image nhận trạng thái `queued`
    - Chuyển sang trạng thái `saving` nghĩa là quá trình tải lên chưa hoàn thành
    - Khi image được tải lên xong, trạng thái image chuyển sang `active`
    - Nếu quá trình tải thất bại thì nó chuyển sang trạng thái `killed` hoặc `deleted`
- Có thể `deactive` (tắt) hoặc `reactive` (bật) các image đã upload thành công bằng command.

<p align=center><img src="https://i.imgur.com/OERBwfx.png"></p>
