### Hướng dẫn cài đặt Imapsync
hướng dẫn cài đặt Imapsync trên hệ điều hành Ubuntu 20.04.

#### Bước 1 – Cài đặt các packages cần thiết
Bắt đầu với việc cài đặt các packages cần thiết cho Imapsync.
```
sudo apt-get install  \
libauthen-ntlm-perl     \
libclass-load-perl      \
libcrypt-ssleay-perl    \
libdata-uniqid-perl     \
libdigest-hmac-perl     \
libdist-checkconflicts-perl \
libencode-imaputf7-perl     \
libfile-copy-recursive-perl \
libfile-tail-perl       \
libio-compress-perl     \
libio-socket-inet6-perl \
libio-socket-ssl-perl   \
libio-tee-perl          \
libmail-imapclient-perl \
libmodule-scandeps-perl \
libnet-dbus-perl        \
libnet-ssleay-perl      \
libpar-packer-perl      \
libreadonly-perl        \
libregexp-common-perl   \
libsys-meminfo-perl     \
libterm-readkey-perl    \
libtest-fatal-perl      \
libtest-mock-guard-perl \
libtest-mockobject-perl \
libtest-pod-perl        \
libtest-requires-perl   \
libtest-simple-perl     \
libunicode-string-perl  \
liburi-perl             \
libtest-nowarnings-perl \
libtest-deep-perl       \
libtest-warn-perl       \
make                    \
cpanminus
```
Tiếp tục cài đặt các modules Python cần thiết.
```
sudo cpanm Crypt::OpenSSL::RSA Crypt::OpenSSL::Random --force
sudo cpanm Mail::IMAPClient JSON::WebToken Test::MockObject 
sudo cpanm Unicode::String Data::Uniqid
```
#### Bước 2 – Cài đặt Imapsync
Download source code từ Github.
```
git clone https://github.com/imapsync/imapsync.git
```
Chạy các lệnh sau để cài đặt.
```
cd imapsync
cp imapsync /usr/bin/
```

Sau bước này, bạn đã sẵn sàng để chuyển dữ liệu email của mình từ một mail server đến một mail server khác bằng giao thức IMAP.

### Hướng dẫn sử dụng Imapsync để chuyển dữ liệu email

Ví dụ về việc chuyển email từ Gmail về server iredmail. Trước khi tiến hành chuyển dữ liệu, bạn cần đảm bảo rằng cả 2 tài khoản email đều có thể truy cập được thông qua IMAP trên server mà bạn cài đặt Imapsync.

#### Bước 1 – Tạo App password tài khoản Gmail

Truy cập và đăng nhập vào link **https://myaccount.google.com/**

Chọn tab **Security > App passwords:**

Phần Select app chọn **Mail, Select Device chọn Other:**

Sau đó điền tên tuỳ ý và nhấn **Generate**:

**Cuối cùng bạn copy và lưu lại App password để sử dụng:**

**Chú ý:** Nếu không thấy phần App pasword là do chưa bật 2fa của mail , bạn cần bật 2fa lên.

#### Bước 2 – Enable IMAP trên tài khoản mail Google
Login vào tài khoản Gmail. Click vào biểu tượng **bánh răng** ở góc phải màn hình và chọn **See All Settings**:

![image](https://user-images.githubusercontent.com/97424062/177459813-10407470-ea3d-44e1-a682-e9ca45cfe717.png)

Chọn tab Forwarding and POP/IMAP sau đó chọn Enable IMAP và nhấn Save Changes để lưu lại cài đặt:
![image](https://user-images.githubusercontent.com/97424062/177249973-85fb595f-4e32-48d5-a140-c3cca40d8cf8.png)

#### Bước 3 – Thực hiện sync email

Trước khi tiến hành chuyển dữ liệu, bạn cần đảm bảo rằng cả 2 tài khoản email đều có thể truy cập được thông qua IMAP trên server mà bạn cài Imapsync. Sau đó bạn có thể chạy câu lệnh đơn giản sau để chuyển email:
```
imapsync --host1 imap.gmail.com   \
         --user1 truongvh@gmail.com 	  \
	 --password1 AppPassword  	  \
	 --ssl1			          \
	 --host2 mail.truongvh.com     \
	 --user2 truongvh1@truongvh.com 	  \
	 --password2 Dest1nat10NPassw0rd  \
	 --ssl2
```
Trong đó:

host1, user1, password1: lần lượt là domain/địa chỉ IP của mail server nguồn (trong tình huống này là imap.gmail.com), tài khoản email và app password đã khởi tạo ở Bước 1.
host2, user2, password2: lần lượt là domain/địa chỉ IP của mail server đích, tài khoản email và password trên mail server đích cần chuyển email đến.

#### Lưu ý
Dùng trực tiếp password sẽ làm lộ pass, thay vào đó hay cho pass vào 1 file và gọi tới chúng.
````
echo 'AppPassword' > passfile1
echo 'p@ssw0rd' > passfile2
````
````
imapsync --host1 imap.gmail.com   \
         --user1 user@domain.com 	  \
	 --passfile1 /path/to/passfile1	  \
	 --ssl1			          \
	 --host2 webmail.vhost.email     \
	 --user2 user@domain.com 	  \
	 --passfile2 /path/to/passfile2   \
	 --ssl2
````
#### Nâng cao
Bạn có thể kết hợp 2 tham số –delete1 và –noexpungeaftereach để thực hiện xoá email trên mail server nguồn để tiện kiểm tra mail đã sync đầy đủ hay chưa và có bị sót email nào hay không:
```
imapsync --host1 imap.gmail.com   \
         --user1 user@domain.com 	  \
	 --passfile1 /path/to/passfile1	  \
	 --ssl1			          \
	 --host2 webmail.vhost.email     \
	 --user2 user@domain.com 	  \
	 --passfile2 /path/to/passfile2   \
         --ssl2                           \
         --delete1 --noexpungeaftereach
```
Ngoài ra, để có thể chạy nhiều tiến trình Imapsync cùng một lúc bạn cần thêm vào tham số sau trên câu lệnh:
```
imapsync --host1 imap.gmail.com   \
         --user1 user@domain.com 	  \
	 --passfile1 /path/to/passfile1	  \
	 --ssl1			          \
	 --host2 webmail.vhost.email     \
	 --user2 user@domain.com 	  \
	 --passfile2 /path/to/passfile2   \
         --ssl2                           \
         --pidfile ""
	 ```
