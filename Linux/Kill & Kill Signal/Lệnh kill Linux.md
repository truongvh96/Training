Lệnh kill trong Linux Để kiểm tra PID các process và show các process có thể sử dụng lệnh ps với option và có thể kết hợp pipeline dữ liệu để tìm kiếm nhanh hơn

VD:

# ps aux | grep mysql
1. Kill process với PID

Cú pháp :

kill SIGNAL PID
2. Các SIGNAL trong Linux

kill -l

0	SIGNULL (NULL)	Null	Kiểm tra quyền truy cập vào PID
1	SIGHUP (HUP)	Hangup	Chấm dứt process, có thể bị kẹt
2	SIGINT (INT)	Interrupt	Chấm dứt process, có thể bị kẹt
3	SIGQUIT (QUIT)	Quit	Chấm dứt bằng core dump, có thể bị mắc kẹt
9	SIGKILL (KILL)	Kill	Buộc chấm dứt process, Signal Kill mạnh nhất
15	SIGTERM (TERM)	Terminate	Giống với SIGNAL 1,2
24	SIGSTOP (STOP)	Stop	Dừng Process
25	SIGTSTP (STP)	Terminal	Dừng Process, có thể bị kẹt
26	SIGCONT (CONT)	Continue	Chạy lại một Process bị Pause
