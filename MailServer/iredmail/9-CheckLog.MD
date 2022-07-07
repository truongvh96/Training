# Check maillog để kiểm tra trạng thái gửi mail.

- File log mail mặc định được tại `/var/log/maillog` hoặc `/var/log/mail.log`  => log này thu thập tổng hợp các hoạt động liên quan đến email tại SMTP server 
- File log iredAPD `/var/log/iredapd/iredapd.log` => log này là log của policy server, có thê check log này để focus thông tin lien quan đến  blacklist blocked
- File log dovecot - lda ( local delivery agent ) : `/var/log/dovecot/lda.log` => log này thể hiện quá trình lưu trữ mail lại local


## 1. Check log gửi mail thành công : 
- Toàn bộ log tại `maillog` trong quá trình gửi một email từ `truongvh1@truongvh.com` -> `truongvh@vccloud.vn` : 
```
Jul  5 17:57:08 mail postfix/smtps/smtpd[128410]: lost connection after CONNECT from unknown[185.220.101.49]
Jul  5 17:57:08 mail postfix/smtps/smtpd[128410]: disconnect from unknown[185.220.101.49] commands=0/0
Jul  5 17:57:14 mail postfix/smtps/smtpd[128410]: connect from tor-exit-45.for-privacy.net[185.220.101.45]
Jul  5 17:57:46 mail postfix/smtps/smtpd[128410]: SSL_accept error from tor-exit-45.for-privacy.net[185.220.101.45]: -1
Jul  5 17:57:46 mail postfix/smtps/smtpd[128410]: warning: TLS library problem: error:1408F10B:SSL routines:ssl3_get_record:wrong version number:../ssl/record/ssl3_record.c:331:
Jul  5 17:57:46 mail postfix/smtps/smtpd[128410]: lost connection after CONNECT from tor-exit-45.for-privacy.net[185.220.101.45]
Jul  5 17:57:46 mail postfix/smtps/smtpd[128410]: disconnect from tor-exit-45.for-privacy.net[185.220.101.45] commands=0/0
Jul  5 17:57:52 mail postfix/smtps/smtpd[128410]: connect from tor-exit-at-the.quesadilla.party[103.251.167.21]
Jul  5 17:58:20 mail postfix/smtps/smtpd[128410]: SSL_accept error from tor-exit-at-the.quesadilla.party[103.251.167.21]: -1
Jul  5 17:58:20 mail postfix/smtps/smtpd[128410]: warning: TLS library problem: error:1408F10B:SSL routines:ssl3_get_record:wrong version number:../ssl/record/ssl3_record.c:331:
Jul  5 17:58:20 mail postfix/smtps/smtpd[128410]: lost connection after CONNECT from tor-exit-at-the.quesadilla.party[103.251.167.21]
Jul  5 17:58:20 mail postfix/smtps/smtpd[128410]: disconnect from tor-exit-at-the.quesadilla.party[103.251.167.21] commands=0/0
Jul  5 17:58:21 mail postfix/smtps/smtpd[128410]: connect from tor-exit-16.zbau.f3netze.de[185.220.100.243]
Jul  5 17:58:54 mail postfix/smtps/smtpd[128410]: SSL_accept error from tor-exit-16.zbau.f3netze.de[185.220.100.243]: -1
Jul  5 17:58:54 mail postfix/smtps/smtpd[128410]: warning: TLS library problem: error:1408F10B:SSL routines:ssl3_get_record:wrong version number:../ssl/record/ssl3_record.c:331:
Jul  5 17:58:54 mail postfix/smtps/smtpd[128410]: lost connection after CONNECT from tor-exit-16.zbau.f3netze.de[185.220.100.243]
Jul  5 17:58:54 mail postfix/smtps/smtpd[128410]: disconnect from tor-exit-16.zbau.f3netze.de[185.220.100.243] commands=0/0
Jul  5 17:59:00 mail postfix/smtps/smtpd[128410]: warning: hostname 185-220-102-7.torservers.net does not resolve to address 185.220.102.7: Name or service not known
Jul  5 17:59:00 mail postfix/smtps/smtpd[128410]: connect from unknown[185.220.102.7]
Jul  5 17:59:04 mail postfix/smtps/smtpd[128410]: SSL_accept error from unknown[185.220.102.7]: -1
Jul  5 17:59:04 mail postfix/smtps/smtpd[128410]: warning: TLS library problem: error:1408F10B:SSL routines:ssl3_get_record:wrong version number:../ssl/record/ssl3_record.c:331:
Jul  5 17:59:04 mail postfix/smtps/smtpd[128410]: lost connection after CONNECT from unknown[185.220.102.7]
Jul  5 17:59:04 mail postfix/smtps/smtpd[128410]: disconnect from unknown[185.220.102.7] commands=0/0
Jul  5 17:59:16 mail postfix/smtps/smtpd[128410]: connect from tor-exit-35.for-privacy.net[185.220.101.35]
Jul  5 17:59:52 mail postfix/smtps/smtpd[128410]: SSL_accept error from tor-exit-35.for-privacy.net[185.220.101.35]: lost connection
Jul  5 17:59:52 mail postfix/smtps/smtpd[128410]: lost connection after CONNECT from tor-exit-35.for-privacy.net[185.220.101.35]
Jul  5 17:59:52 mail postfix/smtps/smtpd[128410]: disconnect from tor-exit-35.for-privacy.net[185.220.101.35] commands=0/0
Jul  5 17:59:55 mail postfix/smtps/smtpd[128487]: connect from tor-exit-46.for-privacy.net[185.220.101.46]
Jul  5 18:00:29 mail postfix/smtps/smtpd[128487]: SSL_accept error from tor-exit-46.for-privacy.net[185.220.101.46]: lost connection
Jul  5 18:00:29 mail postfix/smtps/smtpd[128487]: lost connection after CONNECT from tor-exit-46.for-privacy.net[185.220.101.46]
Jul  5 18:00:29 mail postfix/smtps/smtpd[128487]: disconnect from tor-exit-46.for-privacy.net[185.220.101.46] commands=0/0
Jul  5 20:52:45 mail postfix/submission/smtpd[133565]: connect from unknown[185.167.96.146]
Jul  5 20:52:45 mail postfix/submission/smtpd[133565]: lost connection after UNKNOWN from unknown[185.167.96.146]
Jul  5 20:52:45 mail postfix/submission/smtpd[133565]: disconnect from unknown[185.167.96.146] unknown=0/1 commands=0/1
Jul  5 20:56:05 mail postfix/anvil[133568]: statistics: max connection rate 1/60s for (submission:185.167.96.146) at Jul  5 20:52:45
Jul  5 20:56:05 mail postfix/anvil[133568]: statistics: max connection count 1 for (submission:185.167.96.146) at Jul  5 20:52:45
Jul  5 20:56:05 mail postfix/anvil[133568]: statistics: max cache size 1 at Jul  5 20:52:45
Jul  5 22:23:45 mail postfix/smtps/smtpd[136765]: warning: hostname 207.246.113.16.vultrusercontent.com does not resolve to address 207.246.113.16
Jul  5 22:23:45 mail postfix/smtps/smtpd[136765]: connect from unknown[207.246.113.16]
Jul  5 22:23:45 mail postfix/smtps/smtpd[136765]: Anonymous TLS connection established from unknown[207.246.113.16]: TLSv1.2 with cipher ECDHE-RSA-AES128-GCM-SHA256 (128/128 bits)
Jul  5 22:23:45 mail postfix/smtps/smtpd[136765]: lost connection after CONNECT from unknown[207.246.113.16]
Jul  5 22:23:45 mail postfix/smtps/smtpd[136765]: disconnect from unknown[207.246.113.16] commands=0/0
Jul  5 22:27:06 mail postfix/anvil[136768]: statistics: max connection rate 1/60s for (465:207.246.113.16) at Jul  5 22:23:45
Jul  5 22:27:06 mail postfix/anvil[136768]: statistics: max connection count 1 for (465:207.246.113.16) at Jul  5 22:23:45
Jul  5 22:27:06 mail postfix/anvil[136768]: statistics: max cache size 1 at Jul  5 22:23:45
Jul  6 03:01:05 mail postfix/submission/smtpd[144817]: connect from mail.truongvh.com[127.0.0.1]
Jul  6 03:01:05 mail postfix/submission/smtpd[144817]: Anonymous TLS connection established from mail.truongvh.com[127.0.0.1]: TLSv1.3 with cipher TLS_AES_256_GCM_SHA384 (256/256 bits) key-exchange X25519 server-signature RSA-PSS (4096 bits) server-digest SHA256
Jul  6 03:01:05 mail postfix/submission/smtpd[144817]: NOQUEUE: reject: RCPT from mail.truongvh.com[127.0.0.1]: 554 5.7.1 <truongvh@vccloud.vn>: Recipient address rejected: Blacklisted; from=<truongvh1@truongvh.com> to=<truongvh@vccloud.vn> proto=ESMTP helo=<mail.truongvh.com>
Jul  6 03:01:05 mail postfix/submission/smtpd[144817]: lost connection after RCPT from mail.truongvh.com[127.0.0.1]
A2DB2C9)
Dec 28 15:16:33 mail postfix/qmgr[2688]: 4JNS6h47X1z9p1k: removed
```

- Đoạn log này mô tả quá trình xử lý email gửi đi tại SMTP server **postfix**, có thể nhận biết mail đã được gửi thành công đến SMTP server của bên nhận **captmx-re30.bizflycloud.vn** , đoạn kết nôi và send mail tới bên nhận như sau : 
```
Dec 28 15:16:28 mail postfix/smtp[322145]: Anonymous TLS connection established to captmx-re30.bizflycloud.vn[103.69.195.135]:25: TLSv1.2 with cipher ADH-AES256-GCM-SHA384 (256/256 bits)
Dec 28 15:16:33 mail postfix/smtp[322145]: 4JNS6h47X1z9p1k: to=<truongvh@vccloud.vn>, relay=captmx-re30.bizflycloud.vn[103.69.195.135]:25, delay=5.4, delays=0.01/0.03/0.28/5, dsn=2.0.0, status=sent (250 2.0.0 Ok: queued as 95FA2DB2C9)
```
- Status = sent => email đã được gửi thành công

## 2. Check log quá trình nhận thư thành công : 

- gửi một email test từ `truongvh@vccloud.vn` -> `truongvh1@truongvh.com`, bắt được log tại `maillog` :
```
Jul  7 09:06:14 mail postfix/submission/smtpd[866353]: connect from mail.truongvh.com[127.0.0.1]
Jul  7 09:06:14 mail postfix/submission/smtpd[866353]: Anonymous TLS connection established from mail.truongvh.com[127.0.0.1]: TLSv1.3 with cipher TLS_AES_256_GCM_SHA384 (256/256 bits) key-exchange X25519 server-signature RSA-PSS (4096 bits) server-digest SHA256
Jul  7 09:06:14 mail postfix/submission/smtpd[866353]: 4Ldr9y6wlvz3xgh: client=mail.truongvh.com[127.0.0.1], sasl_method=PLAIN, sasl_username=truongvh1@truongvh.com
Jul  7 09:06:14 mail postfix/cleanup[866357]: 4Ldr9y6wlvz3xgh: message-id=<4c6-62c6a200-d-7d2cc500@266041271>
Jul  7 09:06:15 mail postfix/submission/smtpd[866353]: disconnect from mail.truongvh.com[127.0.0.1] ehlo=2 starttls=1 auth=1 mail=1 rcpt=1 data=1 quit=1 commands=8
Jul  7 09:06:15 mail postfix/qmgr[2502]: 4Ldr9y6wlvz3xgh: from=<truongvh1@truongvh.com>, size=1052, nrcpt=1 (queue active)
Jul  7 09:06:15 mail postfix/10025/smtpd[866377]: connect from mail.truongvh.com[127.0.0.1]
Jul  7 09:06:15 mail postfix/10025/smtpd[866377]: 4Ldr9z5ktdz3yFh: client=mail.truongvh.com[127.0.0.1]
Jul  7 09:06:15 mail postfix/cleanup[866357]: 4Ldr9z5ktdz3yFh: message-id=<4c6-62c6a200-d-7d2cc500@266041271>
Jul  7 09:06:15 mail postfix/10025/smtpd[866377]: disconnect from mail.truongvh.com[127.0.0.1] ehlo=1 mail=1 rcpt=1 data=1 quit=1 commands=5
Jul  7 09:06:15 mail postfix/qmgr[2502]: 4Ldr9z5ktdz3yFh: from=<truongvh1@truongvh.com>, size=2283, nrcpt=1 (queue active)
Jul  7 09:06:15 mail amavis[764113]: (764113-01) Passed CLEAN {RelayedInternal}, ORIGINATING/MYNETS LOCAL [127.0.0.1]:46972 ESMTP/ESMTP <truongvh1@truongvh.com> -> <truongvh@vccloud.vn>, (), Queue-ID: 4Ldr9y6wlvz3xgh, Message-ID: <4c6-62c6a200-d-7d2cc500@266041271>, mail_id: EPPMYA29kEZZ, b: thirEvkIt, Hits: -1.009, size: 1052, queued_as: 4Ldr9z5ktdz3yFh, Subject: "tets", From: <truongvh1@truongvh.com>, User-Agent: SOGoMail_5.7.0, helo=mail.truongvh.com, Tests: [ALL_TRUSTED=-1,HTML_MESSAGE=0.001,T_SCC_BODY_TEXT_LINE=-0.01], autolearn=ham autolearn_force=no, autolearnscore=-0.009, dkim_new=dkim:truongvh.com, 818 ms
Jul  7 09:06:15 mail postfix/amavis/smtp[866358]: 4Ldr9y6wlvz3xgh: to=<truongvh@vccloud.vn>, relay=127.0.0.1[127.0.0.1]:10026, delay=0.96, delays=0.1/0.01/0.03/0.82, dsn=2.0.0, status=sent (250 2.0.0 from MTA(smtp:[127.0.0.1]:10025): 250 2.0.0 Ok: queued as 4Ldr9z5ktdz3yFh)
Jul  7 09:06:15 mail postfix/qmgr[2502]: 4Ldr9y6wlvz3xgh: removed
Jul  7 09:06:17 mail postfix/postscreen[866405]: CONNECT from [123.31.11.244]:51426 to [103.69.194.241]:25
Jul  7 09:06:17 mail postfix/postscreen[866405]: HANGUP after 0 from [123.31.11.244]:51426 in tests before SMTP handshake
Jul  7 09:06:17 mail postfix/postscreen[866405]: DISCONNECT [123.31.11.244]:51426
Jul  7 09:06:19 mail postfix/smtp[866378]: 4Ldr9z5ktdz3yFh: to=<truongvh@vccloud.vn>, relay=captmx-re40.bizflycloud.vn[123.31.11.244]:25, delay=3.2, delays=0.04/0.03/0.26/2.9, dsn=2.0.0, status=sent (250 2.0.0 Ok: queued as 0812D17DA34)

```
- Từ log trên, ta có thể thấy SMTP bên gửi connect tới SMTP bên nhận để gửi mail. SMTP nhận mail, thông qua amavis để kiểm tra sercurity OK => gửi đến dovecot IMAP server 
- Check thêm log `iredapd.log` :
```
Jul  6 08:46:20 mail iredapd [103.69.195.21] RCPT, truongvh@vccloud.vn -> truongvh1@truongvh.com, OK [sasl_username=, sender=truongvh@vccloud.vn, client_name=capt-se165.bizflycloud.vn, reverse_client_name=capt-se165.bizflycloud.vn, helo=capt-se165.bizflycloud.vn, encryption_protocol=TLSv1.2, encryption_cipher=ECDHE-RSA-AES256-GCM-SHA384, server_port=25, process_time=0.0429s]
Jul  6 08:46:20 mail iredapd [103.69.195.21] END-OF-MESSAGE, truongvh@vccloud.vn -> truongvh1@truongvh.com, DUNNO [recipient_count=1, size=699, process_time=0.0113s]

``` 
- Log này thể  hiện viện thông qua `iredapd` để check blacklist/whitelist => OK

- Check log `lda.log` :
```
Jul  7 06:25:06 mail dovecot: lda(postmaster@truongvh.com)<716791><I7TYIEJ8xmL37woAR5v2ug>: sieve: from=root@mail.truongvh.com, envelope_sender=root@mail.truongvh.com, subject=Logwatch for mail.truongvh.com (Linux), msgid=<4Ldmc043H7z3xgh@mail.truongvh.com>, size=7319, delivery_time=91ms, stored mail into mailbox 'INBOX'
```
- Log này thể hiện viện phân phối thư của MDA ( Dovecot ) qua lda, thư thư được lưu về  `INBOX` của `tulha@tulha.fun`


## 3. Check log quá trình nhận/gửi thất bại do blacklist :
- Mình đã set blacklist cho account `truongvh1@truongvh.com` 2 chiều đến đến mail domain `@vccloud.vn`
- Thử gửi test mail từ `truongvh1@truongvh.com` -> `truongvh@vccloud.vn`
- Do đã cấu hình blacklist, iRedAPD chặn thư gửi ra, log `iredadp.log` như sau : 

```
Jul  6 04:17:00 mail iredapd [103.69.195.21] RCPT, truongvh@vccloud.vn -> truongvh2@truongvh.com, REJECT Blacklisted [sasl_username=, sender=truongvh@vccloud.vn, client_name=capt-se165.bizflycloud.vn, reverse_client_name=capt-se165.bizflycloud.vn, helo=capt-se165.bizflycloud.vn, encryption_protocol=TLSv1.2, encryption_cipher=ECDHE-RSA-AES256-GCM-SHA384, server_port=25, process_time=0.1379s]
Jul  6 04:19:36 mail iredapd [103.69.195.21] Bypass greylisting due to SPF match (vccloud.vn)
Jul  6 04:19:36 mail iredapd Whitelisted: wblist=(2, 3, 'W')
```

- Tương tự là log ở `maillog`, verify tại iRedAPD bị `REJECT` , đoạn dưới là trả về HTTP và SMTP error trên roundcube ( giao diện ng dùng) :
```
Dec 29 14:30:37 mail postfix/submission/smtpd[613348]: connect from mail.truongvh.com[127.0.0.1]
Dec 29 14:30:37 mail postfix/submission/smtpd[613348]: Anonymous TLS connection established from mail.truongvh.com[127.0.0.1]: TLSv1.3 with cipher TLS_AES_256_GCM_SHA384 (256/256 bits) key-exchange X25519 server-signature RSA-PSS (4096 bits) server-digest SHA256
Dec 29 14:30:37 mail postfix/submission/smtpd[613348]: NOQUEUE: reject: RCPT from mail.truongvh.com[127.0.0.1]: 554 5.7.1 <quanlh@vccloud.vn>: Recipient address rejected: Blacklisted; from=<truongvh1@truongvh.com> to=<quanlh@vccloud.vn> proto=ESMTP helo=<localhost>
Dec 29 14:30:37 mail roundcube: <v5k60u4i> PHP Error: Invalid response code received from server (POST /mail/?_task=mail&_unlock=loading1640763037540&_framed=1&_lang=en&_action=send)
Dec 29 14:30:37 mail roundcube: <v5k60u4i> SMTP Error: Failed to add recipient 'quanlh@vccloud.vn'. 5.7.1 <quanlh@vccloud.vn>: Recipient address rejected: Blacklisted (Code: 554) in /opt/www/roundcubemail-1.4.11/program/lib/Roundcube/rcube.php on line 1702 (POST /mail/?_task=mail&_unlock=loading1640763037540&_framed=1&_lang=en&_action=send)
Dec 29 14:30:37 mail postfix/submission/smtpd[613348]: disconnect from mail.tuongvh.com[127.0.0.1] ehlo=2 starttls=1 auth=1 mail=1 rcpt=0/1 rset=1 quit=1 commands=7/8
```

- Thử gửi test mail từ `quanlh@vccloud.vn` -> `truongvh1@truongvh.com` : 
- Log chặn mail do blacklist tại `iredadp.log` : 
```
Dec 29 14:37:04 mail iredapd [103.69.195.21] Bypass greylisting due to SPF match (vccloud.vn)
Dec 29 14:37:04 mail iredapd Blacklisted: wblist=(2, 5, 'B')
Dec 29 14:37:04 mail iredapd [103.69.195.21] RCPT, quanlh@vccloud.vn -> truongvh1@truongvh.com, REJECT Blacklisted [sasl_username=, sender=quanlh@vccloud.vn, client_name=capt-se165.bizflycloud.vn, reverse_client_name=capt-se165.bizflycloud.vn, helo=capt-se165.bizflycloud.vn, encryption_protocol=TLSv1.2, encryption_cipher=ECDHE-RSA-AES256-GCM-SHA384, server_port=25, process_time=0.0884s]
```
- Log tại `mail.log` : 
```
Dec 29 14:37:04 mail postfix/smtpd[639025]: connect from capt-se165.bizflycloud.vn[103.69.195.21]
Dec 29 14:37:04 mail postfix/smtpd[639025]: Anonymous TLS connection established from capt-se165.bizflycloud.vn[103.69.195.21]: TLSv1.2 with cipher ECDHE-RSA-AES256-GCM-SHA384 (256/256 bits)
Dec 29 14:37:04 mail postfix/smtpd[639025]: NOQUEUE: reject: RCPT from capt-se165.bizflycloud.vn[103.69.195.21]: 554 5.7.1 <truongvh1@truongvh.com>: Recipient address rejected: Blacklisted; from=<quanlh@vccloud.vn> to=<tulha@tulha.fun> proto=ESMTP helo=<capt-se165.bizflycloud.vn>
Dec 29 14:37:04 mail postfix/smtpd[639025]: disconnect from capt-se165.bizflycloud.vn[103.69.195.21] ehlo=2 starttls=1 mail=1 rcpt=0/1 data=0/1 rset=1 quit=1 commands=6/8
```

## 4. Check log quá trình gửi thất bại do FAIL DMARC : 
- Sửa  rule `DMARC = reject` , sửa các bản ghi `spf` và `dkim` để chúng fail 
- Test gửi mail từ `postmaster@truongvh.com` -> `truongvh@gmail.com`
- Check log thấy thư đã đi ra được ngoài SMTP server, tuy nhiên bị trả về  do fail DMARC, `status = bounced`
```
Dec 29 15:01:07 mail postfix/smtp[732306]: 4JP3kS4kV1z9p1l: to=<truongvh@gmail.com>, relay=gmail-smtp-in.l.google.com[142.251.8.26]:25, delay=2.5, delays=0.07/0.04/1.8/0.58, dsn=5.7.26, status=bounced (host gmail-smtp-in.l.google.com[142.251.8.26] said: 550-5.7.26 Unauthenticated email from truongvh.com is not accepted due 550-5.7.26 to domain's DMARC policy. Please contact the administrator of 550-5.7.26 truongvh.com domain if this was a legitimate mail. Please 550-5.7.26 visit 550-5.7.26  https://support.google.com/mail/answer/2451690 to learn about the 550 5.7.26 DMARC initiative. j24si18976658pll.90 - gsmtp (in reply to end of DATA command))
Dec 29 15:01:07 mail postfix/cleanup[732236]: 4JP3kW1FHCz9p1r: message-id=<4JP3kW1FHCz9p1r@mail.truongvh.com>
Dec 29 15:01:07 mail postfix/bounce[732495]: 4JP3kS4kV1z9p1l: sender non-delivery notification: 4JP3kW1FHCz9p1r
```
