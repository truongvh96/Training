# 1. Cách check mailqueue :
- SMTP server của iredmail là Postfix, vì vậy việc check mail queue sẽ tiến hành trên bộ phận gửi mail này.
- cú pháp : `mailq` hoặc `postqueue -p`
- Ví dụ : 
```
root@mail:~# mailq
----Queue ID----- --Size-- ---Arrival Time---- --Sender/Recipient------
4JMhxB6T8Fz9nsB*      1910 Mon Dec 27 09:50:38 truongvh1@truongvh.com
                                               truongvh@gmail.com

-- 1 Kbytes in 1 Request.

```
- Trong nội dung queue sẽ show được queue ID, Size mail, Thời gian đến SMTP server và địa chỉ gửi/nhận.

# 2. Thao tác khác với mailqueue : 
- View message : `postcat -vq <Queue-ID>`
- Flush queue ( push to send mail in queue ) : 
    -  Flush all mail in queue :  `postqueue -f` or `postfix flush`
    -  Flush single mail witch <Queue-ID> : `postqueue -i <ID>`
- Delete :  
    - Delete all mail in queue : `postsuper -d ALL`
    - Delete single mail in queue : `postsuper -d <Queue-ID>`

Source : 
    - https://linuxhint.com/postfix_mail_queue_management/
    - http://www.postfix.org/QSHAPE_README.html#queues
    
