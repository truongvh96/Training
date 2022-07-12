# Các bản ghi quan trọng của mail : 

# 1. Record A,PTR,MX : 

- A : là bản ghi cốt lõi trong hệ thống DNS, nó có chức năng ánh xạ domain thành địa chỉ IPv4.
- PTR :  có thể gọi đây là bản ghi ngược vì nó có chức năng ánh xạ một IP tới một domain. Bản ghi này hữu ích trong dịch vụ mail server, vì đa phần các nhà cung cấp email lớn đều yêu cầu reverse DNS lookup để verify lại địa chỉ IP mail server nguồn trước khi nhận mail.
- Ví dụ về  A và PTR Record : 
```
A Record : 
truongvh.com A 123.31.11.183
PTR Record : 
123.31.11.183 PTR truongvh.com
```
- Một domain có đủ cấu hình A và PTR Record sẽ có khả năng phân giải tên miền thuận - nghịch . 

- MX - Mail Exchange Record : bản ghi có tác dụng xác định mail server của domain dưới dạng FQDN. Bản ghi này có thông số priority, càng thấp thì mức dộ ưu tiên càng cao, sử dụng trong trường hợp một domain có nhiều mail server.
- Ví dụ : 
```
mail    MX    10    mail.truongvh.com
```

# 2. Record SPF, DKIM, DMARC

## SPF Record
- SPF Record - Sender Policy Framework : là một kỹ thuật xác thực email được sử dụng để chống lại việc giả mạo domain của để giả mạo mail ( Mail Spoofing).
-  SPF Record là bản ghi TXT Record, nó có nhiệm vụ liệt kê các hostname / IP được ủy quyền để thay mặt domain chính gửi email. Nếu thiếu bản ghi này, nhiều khả năng email gửi đi sẽ bị đánh dấu spam. 
- Trước khi tạo bản ghi SPF, chúng ta cần chủân bị tất cả các IP server được sử dụng cho việc gửi mail - Ở đây mình có IP WAN = **123.31.11.183**  được sử dụng trong bài LAB.
- Cấu hình bản ghi SPF : 
    - Tạo bản ghi spf trỏ về **_spf** permit IP WAN : 
    ```
    _spf TXT v=spf1 ip4:123.31.11.183/32 ~all 
    ```
    - Thêm cấu hình spf cho subdomain **mail.truongvh.com** : 
    ```
    mail TXT v=spf1 include:_spf.truongvh.com ~all
    ```
- Có nhiều cách cấu hình SPF record, tham khảo thêm SPF record syntax tại : http://www.open-spf.org/SPF_Record_Syntax/

## DKIM Record 

- DKIM Record - DomainKeys Identified Mail : là một phương pháp xác thực email bằng chữ ký số của tên miền nguồn, trong đó, public key thường được public trên DNS bằng một TXT record. Việc cấu hình DKIM cho domain mail Server giúp thư out không bị đánh giá là spam.
- Khi cài đặt iRedmail với component **amavisd-new**, nó sẽ gen sẵn cho người dùng một cặp key, từ đó ta có thê sử dụng cặp này để cấu hình DKIM
```
root@mail:/etc/amavis# amavisd-new showkeys
; key#1 2048 bits, i=dkim, d=truongvh.com, /var/lib/dkim/truongvh.com.pem
dkim._domainkey.truongvh.com. 3600 TXT (
  "v=DKIM1; p="
  "MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAvqQDqcxtfuS4e8yGK7Y8"
  "Kq7uHq3SZ7mfLKXYZgDFz95EAd8czbLqKMvQeHptptj1gXyRvRpQ5jXmvYbM/RYo"
  "mD2q7FbgMSdzMpIpW2ZQWRHYfgWYIShku6HAVvykcjZzXBAmC1PwYGsFag111axr"
  "lNAnU4RUFcUVW+gYwI34AyToTv11pc9QlYTaSPmUue926XVHvK/HV1eEd0xa5Z8Y"
  "b+NXxA3vUk+Vt2/bwF6iefghq+ZFabS95mxeUzQaO3ub4GbGcZfqYOzU7UuqqyE5"
  "1jGj1SghothjVS8MAx2karpINVmwCI4PJlhV0LTc3xufiH1QWgJcsTmBAQF24bTi"
  "qwIDAQAB")
```
- Ta sử dụng key public ở trên để cấu hình bản tin DKIM trên site quản lý ( bỏ hết các dấu "" ). Cụ thể : **host =  dkim._domainkey, type = TXT, Value = v=DKIM1; p=MIIBIjANBgkqhkiG9w0BAQEFA.....;**


## Demo về cấu hình A,MX,SPF và DKIM : 
- Trỏ domain, câu hình các bản ghi TXT trên site quản lý 
![image](https://user-images.githubusercontent.com/97424062/177903589-e8766590-2d5d-49ab-b61c-532780402566.png)

- Test bản ghi DKIM : 
```
root@mail:/etc/amavis# amavisd-new testkeys
TESTING#1 truongvh.com: dkim._domainkey.truongvh.com => pass
```
- Kiểm tra ba bản ghi TXT đã cấu hình bằng **dig** : 
```
root@tule:~# dig mail.truongvh.com +short  TXT
"v=spf1 include:_spf.truongvh.com ~all"
root@tule:~# dig _spf.truongvh.com +short  TXT
"v=spf1 ip4:123.31.11.183/32 ~all"
root@tule:~# dig dkim._domainkey.truongvh.com +short  TXT
"v=DKIM1; p=MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAvqQDqcxtfuS4e8yGK7Y8Kq7uHq3SZ7mfLKXYZgDFz95EAd8czbLqKMvQeHptptj1gXyRvRpQ5jXmvYbM/RYomD2q7FbgMSdzMpIpW2ZQWRHYfgWYIShku6HAVvykcjZzXBAmC1PwYGsFag111axrlNAnU4RUFcUVW+gYwI34AyToTv11pc9QlYTaSPmUue926XVHvK/H" "V1eEd0xa5Z8Yb+NXxA3vUk+Vt2/bwF6iefghq+ZFabS95mxeUzQaO3ub4GbGcZfqYOzU7UuqqyE51jGj1SghothjVS8MAx2karpINVmwCI4PJlhV0LTc3xufiH1QWgJcsTmBAQF24bTiqwIDAQAB;"
```
- Thử gửi mail tới gmail, xem nội dung thư gốc

## DMARC Record 
- DMARC ( Domain-based Message Authentication, Reporting, and Conformance ) - là phương thức cho phép thiết lập một policy để  xác thực ( authentication ), Reporting ( báo cáo) và đánh giá phù hợp ( Conformance) cho email. 
- Cụ thể, nó chỉ cho Mail Server bên nhận cách xử lý dựa trên kết quả kiểm tra của 2 bản ghi DKIM và SPF. Có thể là hủy bỏ/hoặc đưa vào quarantine ( có thể là thư rác ), đồng thời report lại cho Mail Server bên gửi nếu có một/hoặc cả 2 bản ghi trên FAIL hoặc không được khai báo.
- Luồng xử lý : 

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/DMARC.png>

- Cấu trúc DMARC record : ` v=DMARC1; p=reject; sp=none; adkim=s; aspf=s; rua=mailto:dmarc@mydomain.com; ruf=mailto:dmarc@mydomain.com ` 
- Trong đó 
  - v : version ( hiện tại chỉ hỗ trợ DMARC1)
  - p : policy, chỉ cho Mail Server bên nhận phải làm gì khi xác thực DMARC fail ( có thể chọn `none` - không có chỉ định gì, `quarantine` - chỉ định cách ly, cho vào spambox hoặc `reject` - chỉ định bên nhận reject mail)
  - adkim & aspf : chỉ định mode hoạt động kiểm tra 2 bản ghi DKIM và SPF tương ứng ( có thể chọn `r` - relax mode hoặc `s` - strict mode )
  - sp : chỉ định có áp dụng policy cho tất cả các subdomain thuộc domain gốc không ( options giống với tag `p`)
  - rua : chỉ định email nhận báp cáo tổng quan nếu có FAIL event
  - ruf : chỉ định email nhận báo cáo chi tiết nếu có FAIL

- Demo cấu hình :
  - Cấu hình bản ghi DMARC : 
    - Host : _dmarc
    - Type : TXT
    - Address : v=DMARC1; p=quarantine; adkim=s; aspf=s;   rua=mailto:fail@truongvh.com;  ruf=mailto:fail@truongvh.com;
- Kiểm tra lại với dig : 
```
tuongvh@truongvh-Aspire-A715-71G:/etc$ dig _dmarc.truongvh.com TXT +short
"v=DMARC1; p=quarantine; adkim=s; aspf=s;  rua=mailto:fail@truongvh.com; ruf=mailto:fail@truongvh.com "
```
- Gửi thử mail test hoạt động : 

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/testdmarc.png>

- Mail report từ Receiver ( google gmail ) khi nhận được mail có bản ghi DMARC = FAIL : 

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/fail-report.png>


Source : https://docs.iredmail.org/setup.dns.html
