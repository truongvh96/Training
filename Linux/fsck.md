fsck
fsck (file system consistency check) là một lệnh dùng để kiểm tra filesystem. Có thể sử dụng trong quá trình boot hoặc chạy command manual.

Chạy lệnh fsck cần có quyển root
Nên unmount filesystem trước khi check
Sử dụng fsck
Khi hệ thống lỗi trong quá trình boot.
Filesystem bị hỏng (có thể hiển thị input/output error).
Ổ đĩa, SD card được cắm mà không hoạt động.
Một số option
-A Kiểm tra filesystem lấy từ file /etc/fstab.
-C Hiên thị tiến trình check.
-l Khoá để không program nào sử dụng partition trong kháo trình check.
-M Không check filesystem được mount
-P Kiểm tra các filesystem song song với nhau, bao gồm cả root filesystem
-R Loại trừ root filesystem
-r Hiển thị thống kê
-T Không hiển thị nhãn
-t Chọn cụ thể filesystem được kiểm tra.
-V Cung cấp description
