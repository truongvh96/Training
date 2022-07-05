# Whitelist & Blacklist IredMail 

## Cách thức hoạt động
- iRedMail hỗ trợ 1 script viêt bằng python3 để quản trị viên có thể config, quản trị white/black list.
- Script tại : `/opt/iredapd/tools/wblist_admin.py`
- Show manual : `python3 /opt/iredapd/tools/wblist_admin.py`

## Demo blacklist Inbound : 
- Add blacklist inbound cho user `tulha@tulha.fun` block thư từ `@vccloud.vn` 
```
root@mail:/opt/iredapd/tools# python3 wblist_admin.py --account tulha@tulha.fun --add --blacklist @vccloud.vn
* Establishing SQL connection.
* Add inbound blacklist for account: tulha@tulha.fun
* Add senders: @vccloud.vn
root@mail:/opt/iredapd/tools# python3 wblist_admin.py --account tulha@tulha.fun  --list --blacklist
* Establishing SQL connection.
* List all inbound blacklist for account: tulha@tulha.fun
@vccloud.vn
```

- Thử gửi mail từ tulha@vccloud.vn -> tulha@tulha.fun để test hoạt động :
    - Sent email : 
<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/bl1.png>
    - Mail server @Vccloud trả mail cho tulha@vccloud.vn do bị chặn :
<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/bl2.png>
    - Log chặn của Mail server bên nhận : 
```
Dec 28 11:24:59 mail postfix/smtpd[3599537]: connect from capt-se165.bizflycloud.vn[103.69.195.21]
Dec 28 11:24:59 mail postfix/smtpd[3599537]: Anonymous TLS connection established from capt-se165.bizflycloud.vn[103.69.195.21]: TLSv1.2 with cipher ECDHE-RSA-AES256-GCM-SHA384 (256/256 bits)
Dec 28 11:24:59 mail postfix/smtpd[3599537]: NOQUEUE: reject: RCPT from capt-se165.bizflycloud.vn[103.69.195.21]: 554 5.7.1 <tulha@tulha.fun>: Recipient address rejected: Blacklisted; from=<tulha@vccloud.vn> to=<tulha@tulha.fun> proto=ESMTP helo=<capt-se165.bizflycloud.vn>
Dec 28 11:24:59 mail postfix/smtpd[3599537]: disconnect from capt-se165.bizflycloud.vn[103.69.195.21] ehlo=2 starttls=1 mail=1 rcpt=0/1 data=0/1 rset=1 quit=1 commands=6/8
```

## Demo Blacklist outbound 
- Add blacklist outbound cho user `tulha@tulha.fun` block thử gửi tới `@vccloud.vn` : 
```
root@mail:/opt/iredapd/tools# python3 wblist_admin.py --outbound  --account tulha@tulha.fun --add --blacklist @vccloud.vn
* Establishing SQL connection.
* Add outbound blacklist for account: tulha@tulha.fun
* Add senders: @vccloud.vn
```
- Thử gửi mail từ tulha@tulha.fun -> tulha@vccloud.vn
    - Thử bị block ngay tại step gửi user interface : 
<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/bl3.png>
    - Log chặn tại mail server :

```
Dec 28 11:45:37 mail postfix/submission/smtpd[3676640]: NOQUEUE: reject: RCPT from mail.tutrangcubeoxam.online[127.0.0.1]: 554 5.7.1 <tulha@vccloud.vn>: Recipient address rejected: Blacklisted; from=<tulha@tulha.fun> to=<tulha@vccloud.vn> proto=ESMTP helo=<mail.tutrangcubeoxam.online>
```

## Demo Whitelist in&outbound 

- Với demo trên, email `tulha@tulha.fun` đã bị chặn 2 chiều với `@vccloud.vn`, mình sẽ thêm rule whitelist cho phép giao tiếp với email `tulha@vccloud.vn` : 
```
python3 wblist_admin.py --outbound  --account tulha@tulha.fun --add --whitelist tulha@vccloud.vn
python3 wblist_admin.py --account tulha@tulha.fun --add --whitelist tulha@vccloud.vn
```
- Giờ thử gửi/nhận tới `tulha@vccloud.vn` => OK

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/wl1.png>

- Thử gửi tới một email khác thuộc domain @vccloud.vn => FAIL
    - Trace log thấy bị chặn trên svr do outbound blacklisted
```
Dec 28 14:37:23 mail postfix/submission/smtpd[163632]: NOQUEUE: reject: RCPT from mail.tutrangcubeoxam.online[127.0.0.1]: 554 5.7.1 <quanlh@vccloud.vn>: Recipient address rejected: Blacklisted; from=<tulha@tulha.fun> to=<quanlh@vccloud.vn> proto=ESMTP helo=<localhost>
```
- Thử gửi từ `quanlh@vccloud` -> `tulha@tulha.fun` => FAIL
    - Trace log thấy bị svr chặn do inbound blacklisted 
```
Dec 28 14:39:23 mail postfix/smtpd[176572]: connect from capt-se185.bizflycloud.vn[103.69.195.78]
Dec 28 14:39:23 mail postfix/smtpd[176572]: Anonymous TLS connection established from capt-se185.bizflycloud.vn[103.69.195.78]: TLSv1.2 with cipher ECDHE-RSA-AES256-GCM-SHA384 (256/256 bits)
Dec 28 14:39:23 mail postfix/smtpd[176572]: NOQUEUE: reject: RCPT from capt-se185.bizflycloud.vn[103.69.195.78]: 554 5.7.1 <tulha@tulha.fun>: Recipient address rejected: Blacklisted; from=<quanlh@vccloud.vn> to=<tulha@tulha.fun> proto=ESMTP helo=<capt-se185.bizflycloud.vn>
```



- Source : https://docs.iredmail.org/amavisd.wblist.html
