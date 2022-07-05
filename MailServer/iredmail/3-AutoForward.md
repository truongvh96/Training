# Auto Forward 
- Auto forward  là tính năng rất hữu ích, sử dụng khi bạn sử dụng nhiều email cùng một lúc, giúp bạn check được mail từ 2 hoặc nhiều hơn email tại một một duy nhất.

- Tính năng này được setup trên `iRed-AdminPro` hoặc câu hình trong `Database` của mail server.

# Demo 

- Mô hình của mình sử dung DB : `MYSQL Server`  
- bản chất là insert thêm vào bảng `forwardings` của DB `vmail`. 
- Thao tác trên Mysql server, câu lệnh như sau : 
```
USE vmail;
INSERT INTO forwardings (address,
                         forwarding,
                         domain,
                         dest_domain,
                         is_forwarding,
                         active)
                 VALUES ('user@domain.com',
                         'forward@example.com',
                         'domain.com',
                         'example.com',
                         1,
                         1);
```
- Trường hợp này, Mình muốn forward thư nhận từ email   `tulha-support@tulha.fun` -- > `tulha@tulha.fun`, thực hiện như sau :
```
MariaDB [vmail]> USE vmail;
Database changed
MariaDB [vmail]> INSERT INTO forwardings (address,
    ->                          forwarding,
    ->                          domain,
    ->                          dest_domain,
    ->                          is_forwarding,
    ->                          active)
    ->                  VALUES ('tulha-support@tulha.fun',
    ->                          'tulha@tulha.fun',
    ->                          'tulha.fun',
    ->                          'tulha.fun',
    ->                          1,
    ->                          1);
Query OK, 1 row affected (0.023 sec)

```

- Thử gửi 1 email tới `tulha-support@tulha.fun` để test .

- Ta có thể đồng thời cấu hình một email forward đến nhiều email khác, hoặc nhiều email forward vào một mail .

- Tham khảo thêm cấu hình nêu dùng DB `LDAP` : https://docs.iredmail.org/ldap.user.mail.forwarding.html

Source : https://docs.iredmail.org/sql.user.mail.forwarding.html    
