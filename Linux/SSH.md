#### SSH hoạt động thế nào
- SSH Key hoạt động chứng thực người dùng truy cập bằng cách đối chiếu giữa Private Key và Public Key.
- Public Key có thể coi như ổ khoá lưu ở phía Server còn Private Key là chìa khoá nằm ở phía Client.
#### Các thành phần của SSH Key
- Public Key (file lưu dữ liệu string) : Key này sẽ sử dụng để copy vào file ~/.ssh/authorized_keys trên phía Server Listen SSH (Có thể dùng chung Public Key cho nhiều Server SSH).
- Private Key (file lưu dữ liệu string) : FIle này được lưu ở máy Client
- Keypharse (Dạng string và cần ghi nhớ): Đây là mật khẩu để mở Private Key khi đăng nhập sẽ hỏi (nếu được kích hoạt lúc sinh SSH Key).
##### 1. SSH Key
Sử dụng Key gen để sinh khoá cho SSH
``` ssh-keygen -t rsa ``` Lệnh này sẽ tạo ra một cặp SSH key sử dụng thuật toán mã hoá RSA
Người dùng cần chỉ định file path để lưu cặp key Public và Private. Thư mục mặc định ``` $HOME/.ssh ```
```
truong@dell:~/.ssh$ ll

total 20
drwx------  2 truong truong 4096 Jun 28 15:00 ./
drwxr-xr-x 33 truong truong 4096 Jun 26 07:12 ../
-rw-------  1 truong truong 1766 Jun 28 14:57 id_rsa
-rw-r--r--  1 truong truong  392 Jun 28 14:57 id_rsa.pub
-rw-r--r--  1 truong truong 1550 Jun 27 09:02 known_hosts
```
Nhập Passpharse nếu sử dụng (và cần ghi nhớ password này ).
Copy public key id_rsa.pub vào Server với đường dẫn $HOME/.ssh/authorized_keys có thể dùng lệnh ssh-copy-id
```
truong@ansible_lab:~/.ssh$ cat authorized_keys 
ssh-rsa BAAAB3NzaC1yc2EAAAADAQABAAABAQC+vsRiHhMnYNkJ1BqJA80/uXMoC21cajGi/X5sgSJsUG5+hQDWd7mj/tujHXNMHK4WskwoDDO2MH6PEWeJ3HHzAlMr6hnKt645GnfhA9Y4CF5AzHNvVF+Aqrr+gpop7PYWwoOm46BrSuAV6oa5rzKKdXn4qCGgKFbiahHxD2NmG8+EzhBgc9xJQm8XBu5fZvZ8RA3qy1w9FUzKtLTrHJt9rAaWw+S4LrNzzhviTzqV/QrZGIsStx3ixdYXI8p5kHHuaDfj6RaxzzfrZZ/zE2OhV8t/a0HBOKJ7BizfmiWlpo6Wp9IR/CCapshbuu4yOhonkVdTFy6x6acMGjxnsYfj truong@dell
```
Kiểm tra kết nối đến server ssh user@hostname Điền Passpharse và kết nối sẽ được khởi tạo.
##### 2. SSH Agent
SSH Key sử dụng Passpharse sẽ luôn hỏi password passpharse khi tạo connect Để tránh điều này có thể sử dụng ssh-agent (một chương trình chạy dưới background và lưu trữ key trong ram) mỗi lần ssh sử dụng key sẽ không phải nhập passpharse.

Enable ssh-agent
truong@dell:~/.ssh$ eval "$(ssh-agent -s)"
Agent pid 10038

Add SSH key và ssh-agent
truong@dell:~/.ssh$ ssh-add ~/.ssh/id_rsa
Enter passphrase for /home/truong/.ssh/id_rsa: 
Identity added: /home/truong/.ssh/id_rsa (/home/truong/.ssh/id_rsa)
##### 3. SSH Agent Forwarding

Được sử dụng để forward key SSH thông qua 1 server trung gian (SSH Agent Server)

VD: Có 01 Web Server có thể truy cập từ Internet và 02 Database Server - Miền Internal chỉ có IP Private thông tới Web Server .

Client lúc này cần SSH tới Database Server , nhưng giả sử Client không có connect tới vừng mạng Internal của Database Server. Lúc này cần Web Server đứng ra làm trung gian cho việc kết nối SSH.

Nhưng vì Web Server nằm ở vùng mạng public nên việc lưu private key dùng cho việc authenticate SSH Key tới Database Server là không an toàn.

Do đó các private key dùng SSH tới các Database Server c được lưu ở phía Client.

Khi có request SSH từ Web Server tới những Database Server này sẽ hỏi khóa private cho việc xác thực, lúc này cần SSH Forward ra tay để chuyển yêu cầu và gửi Private Key từ phía Client lên để xác thực v

Cấu hình trên máy Client

Sửa file ~/.ssh/config thêm dòng sau

Host 192.168.254.240 #IP Web Server

  ForwardAgent yes
  
Hoặc có thể chạy trực tiếp lệnh : ``` ssh -A user@192.168.254.240 ```

Từ đây Web Server có thể SSH tới các Database Server (với điều kiện trên Database Server cần phải có Public Key kh Client).
##### 4. SCP
Sử dụng secure copy để truyền file trong network được mã hoá VD:

Copy file từ local tới máy remote
```
scp myfile.tar.gz truong@10.0.4.243:/home/tony/
```
Copy từ remote về folder hiện tại trên local sử dụng ký tự .
```
scp truong@10.0.4.243:/home/tony/newfile.tar.gz .
``
Copy tới máy remote sử dụng public key id_rsa.pub
```
scp id_rsa.pub tony@10.0.4.243:/home/truong
```
