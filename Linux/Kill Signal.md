SIGTERM là gì ?

SIGTERM được coi là một hình thức “soft kill”, bởi vì khi tiến trình nhận được tín hiệu SIGTERM thì tiến trình đó có thể được phép bỏ qua hoạt động dừng tiến trình.

Điều đó có nghĩa, khi một ứng dụng được lập trình thường sẽ có phần lập trình xử lý với các signal SIGTERM từ hệ điều hành. Quá trình này thường được gọi là “Graceful shutdown“. Nên nếu ứng dụng không có lập trình để xử lý các signal SIGTERM hoặc cố tình không xử lý khi nhận tín hiệu SIGTERM thì cho dù bạn cố gắng gửi hàng trăm tín hiệu SIGTERM thì cũng vô vọng trong việc kill đi một process trên Linux.

Vậy thì một tiến trình nhận SIGTERM sẽ có 3 trạng thái dễ thấy:

Không phản ứng gì cả.
Dừng ngay lập tức
Một thời gian ngắn sau sẽ dừng hoạt động. Trong thời gian đó tiến trình sẽ dọn dẹp các resource như database connection, tiến trình con,… (graceful shutdown).
Gửi tín hiệu SIGTERM như thế nào ?
Người sử dụng Linux có thể chủ động gửi tín hiệu SIGTERM đến với process có process id đã được xác định.

Cú pháp:

# kill -15 <process_id>
SIGKILL là gì ?
Tín hiệu SIGKILL được sử dụng để chấm dứt quá trình hoạt động của một tiến trình ngay lập tức. Tín hiệu này không thể bị từ chối hay bị ngăn cản bởi tiến trình được chỉ định. Đơn giản là vì tín hiệu sẽ đi thẳng đến kernel init. Từ đó Kernel init tiến hành dừng tiến trình ngay.

Tuy nhiên, không phải lúc nào kernel cũng có thể dừng quá trình hoạt động của tiến trình thành công trong vài tình huống. Ví dụ như tiến trình đang chờ đợi disk io, network,… từ đó kernel không có khả năng dừng tiến trình được. Khi đấy sẽ sinh ra các Zombie process ở trạng thái uninterruptible sleep.

Gửi tín hiệu SIGKILL như thế nào ?
Cú pháp:

# kill -9 <process_id>
Sự khác nhau giữa SIGTERM và SIGNAL trong Linux
Cả 2 tín hiệu signal đều sử dụng để chấm dứt hoạt động của một tiến trình trong Linux, vài điểm khác biệt giữa cả 2 được tóm gọn như sau :

SIGTERM yêu cầu tiến trình dừng hoạt động. Còn SIGKILL thì chấm dứt hoạt động của tiến trình ngay lập tức.
SIGTERM có thể được xử lý hoặc từ chối xử lý quá trình dừng hoạt động bởi tiến trình. Còn SIGKILL thì không tiến trình có cơ hội ngăn chặn hay từ chối cả.
SIGTERM sẽ không dừng hoạt động của tiến trình con. Còn SIGKILL sẽ dừng hoạt động cả tiến trình cha và tiến trình con nếu có.
SIGKILL dễ tạo ra các tiến trình ma (zombie process) vì tiến trình bị ngừng hoạt động sẽ không có cơ hội thông báo cho tiến trình cha hoặc tiến trình con về việc bị ngừng hoạt động.
Thông thường cách dừng hoạt động một tiến trình đúng đắn nên là dùng SIGTERM trước. Trừ khi tiến trình đó không phản hồi, hoặc không xử lý SIGTERM thì kill bằng SIGKILL.
