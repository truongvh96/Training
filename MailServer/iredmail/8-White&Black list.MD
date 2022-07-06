# Whitelist & Blacklist IredMail 

## Cách thức hoạt động
- iRedMail hỗ trợ 1 script viêt bằng python3 để quản trị viên có thể config, quản trị white/black list.
- Script tại : `/opt/iredapd/tools/wblist_admin.py`
- Show manual : `python3 /opt/iredapd/tools/wblist_admin.py`

## Demo blacklist Inbound : 
- Add blacklist inbound cho user `tulha@tulha.fun` block thư từ `@vccloud.vn` 
```
root@mail:/opt/iredapd/tools# python3 wblist_admin.py --account truongvh1@truongvh.com --add --blacklist @vccloud.vn
* Establishing SQL connection.
* Add inbound blacklist for account: truongvh1@truongvh.com
* Add senders: @vccloud.vn
root@mail:/opt/iredapd/tools# python3 wblist_admin.py --account truongvh1@truongvh.com  --list --blacklist
* Establishing SQL connection.
* List all inbound blacklist for account: truongvh1@truongvh.com
@vccloud.vn
```

- Thử gửi mail từ truongvh@vccloud.vn -> truongvh1@truongvh.com để test hoạt động :
    - Sent email : 

    - Mail server @Vccloud trả mail cho truongvh1@truongvh.com do bị chặn :

    - Log chặn của Mail server bên nhận : 
```
Dec 28 11:24:59 mail postfix/smtpd[3599537]: connect from capt-se165.bizflycloud.vn[103.69.195.21]
Dec 28 11:24:59 mail postfix/smtpd[3599537]: Anonymous TLS connection established from capt-se165.bizflycloud.vn[103.69.195.21]: TLSv1.2 with cipher ECDHE-RSA-AES256-GCM-SHA384 (256/256 bits)
Dec 28 11:24:59 mail postfix/smtpd[3599537]: NOQUEUE: reject: RCPT from capt-se165.bizflycloud.vn[103.69.195.21]: 554 5.7.1 <truongvh1@truongvh.com>: Recipient address rejected: Blacklisted; from=<truongvh@vccloud.vn> to=<truongvh1@truongvh.com> proto=ESMTP helo=<capt-se165.bizflycloud.vn>
Dec 28 11:24:59 mail postfix/smtpd[3599537]: disconnect from capt-se165.bizflycloud.vn[103.69.195.21] ehlo=2 starttls=1 mail=1 rcpt=0/1 data=0/1 rset=1 quit=1 commands=6/8
```

## Demo Blacklist outbound 
- Add blacklist outbound cho user `tulha@tulha.fun` block thử gửi tới `@vccloud.vn` : 
```
root@mail:/opt/iredapd/tools# python3 wblist_admin.py --outbound  --account truongvh1@truongvh.com --add --blacklist @vccloud.vn
* Establishing SQL connection.
* Add outbound blacklist for account: truongvh1@truongvh.com
* Add senders: @vccloud.vn
```
- Thử gửi mail từ truongvh1@truongvh.com -> truongvh@vccloud.vn
    - Thử bị block ngay tại step gửi user interface : 
![image](https://user-images.githubusercontent.com/97424062/177458890-1a465a0d-12bf-45f5-9cb9-a774f822d912.png)
    - Log chặn tại mail server :

```
Dec 28 11:45:37 mail postfix/submission/smtpd[3676640]: NOQUEUE: reject: RCPT from mail.truongvh.com[127.0.0.1]: 554 5.7.1 <truongvh@vccloud.vn>: Recipient address rejected: Blacklisted; from=<tulha@tulha.fun> to=<truongvh@vccloud.vn> proto=ESMTP helo=<mail.truongvh.com>
```

## Demo Whitelist in&outbound 

- Với demo trên, email `tulha@tulha.fun` đã bị chặn 2 chiều với `@vccloud.vn`, mình sẽ thêm rule whitelist cho phép giao tiếp với email `truongvh@vccloud.vn` : 
```
python3 wblist_admin.py --outbound  --account truongvh1@truongvh.com --add --whitelist truongvh@vccloud.vn
python3 wblist_admin.py --account truongvh1@truongvh.com --add --whitelist truongvh@vccloud.vn
```
- Giờ thử gửi/nhận tới `truongvh@vccloud.vn` => OK


- Thử gửi tới một email khác thuộc domain @vccloud.vn => FAIL
    - Trace log thấy bị chặn trên svr do outbound blacklisted
```
Dec 28 14:37:23 mail postfix/submission/smtpd[163632]: NOQUEUE: reject: RCPT from mail.truongvh.com[127.0.0.1]: 554 5.7.1 <quanlh@vccloud.vn>: Recipient address rejected: Blacklisted; from=<truongvh1@truongvh.com> to=<quanlh@vccloud.vn> proto=ESMTP helo=<localhost>
```
- Thử gửi từ `quanlh@vccloud` -> `truongvh1@truongvh.com` => FAIL
    - Trace log thấy bị svr chặn do inbound blacklisted 
```
Dec 28 14:39:23 mail postfix/smtpd[176572]: connect from capt-se185.bizflycloud.vn[103.69.195.78]
Dec 28 14:39:23 mail postfix/smtpd[176572]: Anonymous TLS connection established from capt-se185.bizflycloud.vn[103.69.195.78]: TLSv1.2 with cipher ECDHE-RSA-AES256-GCM-SHA384 (256/256 bits)
Dec 28 14:39:23 mail postfix/smtpd[176572]: NOQUEUE: reject: RCPT from capt-se185.bizflycloud.vn[103.69.195.78]: 554 5.7.1 <truongvh1@truongvh.com>: Recipient address rejected: Blacklisted; from=<quanlh@vccloud.vn> to=<truongvh1@truongvh.com> proto=ESMTP helo=<capt-se185.bizflycloud.vn>
```



- Source : https://docs.iredmail.org/amavisd.wblist.html
