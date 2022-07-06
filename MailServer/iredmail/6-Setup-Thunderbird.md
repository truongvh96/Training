# Setup iRedmail with Thunderbird Pop3/IMAP

# 1. Login Thunderbird 
- Bật thunderbird , chọn `Files -> New -> Existing Mail Account...` 
- Điền chính xác địa chỉ email và password tại hộp thoại, sau đó click `configure manually...` để config IMAP/POP3 và SMTP server.

![image](https://user-images.githubusercontent.com/97424062/177455692-de96dc23-c81a-42cc-83f6-19272802e37a.png)

- Ở đây xác định địa chỉ đã trỏ trên DNS của 2 server `SMTP` và `IMAP/POP3` và 2 port tương ứng. Trường hợp này mình cài đặt SMTP và IMAP Server chung, các port tương ứng là `587` cho SMTP và `143` cho IMAP/ hoặc `110` cho POP3, nên điền tương ứng như sau, sau đó click `re-test` để  validate config : 
![image](https://user-images.githubusercontent.com/97424062/177456350-dc3a8269-9cb3-4e86-88a0-eda46018d6c4.png)
- Sau khi `re-test` thành công, click `Done` để  thunderbird tiến hành check email password => sau bước này bạn đã có thể sử dụng thunderbird để gửi, nhận mail
- Đăng nhập thành công 2 account trên Thunderbird : 
![image](https://user-images.githubusercontent.com/97424062/177456181-b3350aae-0f18-4899-b2a8-3403e451a4f9.png)

# 2. Login Outlook 
- Các step tương tự như trên Thunderbird
- Tham khảo thêm cách login trên Outlook & Mobile tại : https://docs.bizflycloud.vn/business_email/howtos/setting_mail_client/

Source : https://docs.iredmail.org/configure.thunderbird.html
