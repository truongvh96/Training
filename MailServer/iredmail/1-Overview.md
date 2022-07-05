# 1. Các giao thức và thành phần cơ bản  : 
- Các giao thức cơ bản : 
    - SMTP : Simple Mail Tranfer Protocol - Giao thức nền tảng trong quá trình gửi, nhận mail - đóng vài trò vận chuyển email trên mạng internet cũng như trong mạng local.
    - POP/POP3 : Post Office Protocol - Giao thức sử dụng trong quá trình nhận mail, đồng bộ mail lên mailbox của người nhận. Cách thức hoạt động cơ bản của giao thức này là download toàn bộ mail của ngươi nhận -> local mailbox -> sau đó xóa bỏ đi các mail đã download tại server mailbox ( có option không xóa trên server)
    - IMAP : Internet Mail Access Protocol - Giao thức này hoạt động trong quá trình nhận mail như POP, nhưng không download toàn bộ mail xuống local mailbox, mà chỉ ánh xạ dạng shortcut mail từ server mailbox xuống mailbox của người dùng, không xóa mail tại server.
- Các thành phần cơ bản :
    - Mail User Agent ( MUA ) : Thành phần tương tác trực tiếp với end-user, ví dụ : Phần mềm như thunderbird,outlook, ... Web interface như mail.google.com, mail.yahoo.com,.. 
    - Mail Tranfer Agent ( MTA ) : Thành phần chịu trách nhiệm chuyển mail từ mail server nguồn -> đích. VD : Postfix, Sendmail. 
    - Mail Delivery Agent ( MDA ) : Nếu MTA là đơn vị vận chuyển quốc tế  thì MDA có vai trò vận chuyển địa phương, nó nhận email từ MTA -> sắp xếp, vận chuyển tới mailbox của người nhận. VD : Dovecot, Cyrus IMAP.
- Mô tả quá trình gửi - nhận mail : 

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/20.png>

- Ngoài các thành phần cơ bản trên, một hệ thống mail còn có nhiều thành phần phụ trợ khác như : content filter, antispam, antivirus, fail2ban, monitor, v.v
 
# 2. iRedMail là gì : 
- iRedMail là một nền tảng mailserver sử dụng các thành phần opensource. Nó sử dụng shell script để tự động hóa cài đặt và cấu hình các thành phần cho một mail server trên server Linux/BSD. Với iRedMail, việc cài đặt  và cấu hình mail server trở nên dễ dàng.
- Các thành phần :
    - Postfix : MTA ( SMTP server )
    - Dovecot : MDA ( IMAP server )
    - Nginx : Web server 
    - OpenLDAP, MySQL/MariaDB, or PostgreSQL  : Database to storage app data + mail accounts 
    - amavisd-new : Content Filter 
    - SpamAssassin : Anti-spam
    - ClamAV : Anti-Virus
    - Roundcube : MUA
    - SOGo : MUA nhưng nhiều chức năng hơn  
    - Fail2ban : ngăn xâm nhập trái phép 
    - mlmmj : mailing list manager
    - Netdata : monitoring các thông số server
    - iRedAPD : policy server for greylisting


# 3. Mô tả hoạt động : 
- Mô tả các thành phần hoạt động : 
<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/big.picture.png> 

- Postfix : MTA , đóng vai trò gửi mail ra và là nơi đầu tiên nhận mail mới gửi vào. Đảm bảo việc gửi ra qua giao thức SMTP . Đảm bảo việc nhận thư vào, filter qua các bộ lọc ban đầu như : blacklist, user authen, greylist,... để xác định việc nhận hay drop email đến. Sau đó chuyển tiếp qua **AMaViS** để scan spam / virus , nhận lại và chuyển cho MDA. 
- Dovecot : MDA , đóng vai trò IMAP server, tiếp nhận mail từ MTA ( Postfix ), storage tại maildirs, tiếp nhận và phân phối mail tới MUA của người dùng bằng giao thức POP3/IMAP.
- AMaViS : Giống như trung tâm security của hệ thống mail, email sẽ được scan spam ( SpamAssasin) và virus ( ClamAV) tại đây.

Source : https://docs.iredmail.org/used.components.html
