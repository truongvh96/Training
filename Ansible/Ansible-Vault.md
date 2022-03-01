#### Ansible Vault là gì?

Vault là một cơ chế cho phép nội dung được mã hóa được kết hợp một cách minh bạch vào quy trình làm việc Ansible. Một tiện ích có tên ansible-vault bảo mật dữ liệu bí mật bằng cách mã hóa nó trên đĩa.

Vault được triển khai với mức độ chi tiết ở cấp độ file. Nó sử dụng thuật toán AES256 để cung cấp mã hóa đối xứng được khóa cho password do user cung cấp. Điều này nghĩa là cùng một password được sử dụng để mã hóa và giải mã nội dung, điều này rất hữu ích từ quan điểm khả năng sử dụng. Ansible có thể xác định và giải mã các file nào được mã hóa vault mà nó tìm thấy trong khi thực hiện một playbook hoặc tác vụ.

#### Cách quản lý file nhạy cảm với ansible-vault

***Tạo file được mã hóa mới***

Để tạo một file mới được mã hóa bằng Vault, hãy sử dụng lệnh ``` ansible-vault create 'Tên file' ``` .
Ví dụ:
```
ansible-vault create vault.yml
```
Sẽ được yêu cầu nhập và xác nhận password.

Để kiểm tra chức năng mã hóa, hãy nhập một số văn bản kiểm tra:

Secret information 

Ansible sẽ mã hóa nội dung khi đóng file . Nếu kiểm tra file , thay vì nhìn thấy các từ đã nhập, sẽ thấy một khối được mã hóa:

cat vault.yml
```
Output
$ANSIBLE_VAULT;1.1;AES256 65316332393532313030636134643235316439336133363531303838376235376635373430336333 3963353630373161356638376361646338353763363434360a363138376163666265336433633664 30336233323664306434626363643731626536643833336638356661396364313666366231616261 3764656365313263620a383666383233626665376364323062393462373266663066366536306163 31643731343666353761633563633634326139396230313734333034653238303166
```

#### Mã hóa các file hiện có

Có thể mã hóa file hiện có bằng lệnh :
```
ansible-vault encrypt encrypt_me.txt
```
, Yêu cầu cung cấp và xác nhận password . Sau đó, một thông báo sẽ xác nhận mã hóa:

Kiểm tra file , ta sẽ thấy một mẫu mã hóa tương tự:

cat encrypt_me.txt
```
Output
$ANSIBLE_VAULT;1.1;AES256 66633936653834616130346436353865303665396430383430353366616263323161393639393136 3737316539353434666438373035653132383434303338640a396635313062386464306132313834 34313336313338623537333332356231386438666565616537616538653465333431306638643961 3636663633363562320a613661313966376361396336383864656632376134353039663662666437 39393639343966363565636161316339643033393132626639303332373339376664
```
Ansible mã hóa nội dung hiện có giống như cách nó mã hóa các file mới.

Xem file được mã hóa:
```
ansible-vault view vault.yml
```
Output
```
Vault password: Secret information
```

#### Chỉnh sửa file được mã hóa

Lệnh ansible-vault edit :
```
ansible-vault edit vault.yml
```

#### Giải mã thủ công các file được mã hóa

Để giải mã file được mã hóa vault, hãy sử dụng lệnh ``` ansible-vault decrypt ```.


Nhập tên của file được mã hóa:
```
ansible-vault decrypt vault.yml
```
Output
```
Vault password: Decryption successful
```

#### Thay đổi password của các file được mã hóa

Sử dụng lệnh ``` ansible-vault rekey ``` :
```
ansible-vault rekey encrypt_me.txt
```

#### Chạy Ansible với các file được mã hóa Vault

Cách đơn giản nhất để giải mã nội dung trong thời gian chạy là Ansible nhắc về thông tin đăng nhập thích hợp. có thể thực hiện việc này bằng cách thêm --ask-vault-pass vào bất kỳ ansible hoặc ansible-playbook . Ansible sẽ nhắc nhập password mà nó sẽ sử dụng để cố gắng giải mã mọi nội dung được bảo vệ bởi vault mà nó tìm thấy.

Ví dụ:
```
ansible --ask-vault-pass -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost 
```
### Sử dụng Ansible Vault với file password

Nếu không muốn nhập password Vault mỗi khi thực thi một tác vụ, có thể thêm password Vault vào một file và tham chiếu file trong quá trình thực thi.

Ví dụ: Đặt password của trong file .vault_pass như sau:
```
echo 'my_vault_password' > .vault_pass 
```
Nếu đang sử dụng kiểm soát version , hãy đảm bảo thêm file password vào file bỏ qua của phần mềm kiểm soát version để tránh vô tình phạm phải:
```
echo '.vault_pass' >> .gitignore 
```
Bây giờ, có thể tham chiếu file thay thế. --vault-password-file có sẵn trên dòng lệnh. Ta có thể hoàn thành nhiệm vụ tương tự từ phần cuối cùng bằng lệnh :
```
ansible --vault-password-file=.vault_pass -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost
```
Sẽ không được yêu cầu nhập password Vault lần này.

#### Tự động đọc file password

Để tránh phải cung cấp cờ, có thể đặt biến môi trường ANSIBLE_VAULT_PASSWORD_FILE với đường dẫn đến file password :
```
export ANSIBLE_VAULT_PASSWORD_FILE=./.vault_pass
```
Đến đây có thể thực thi lệnh mà không có --vault-password-file cho phiên hiện tại:
```
ansible -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost 
```
Để làm cho Ansible biết vị trí file password qua các phiên, có thể chỉnh sửa file ``` ansible.cfg ```.

Mở file ansible.cfg local mà ta đã tạo trước đó:
```
[defaults]
vault_password_file = ~/.vault_pass
```

#### Đọc password từ một biến môi trường

Mở file .vault_pass trong editor :
```
vi .vault_pass
```
Thay thế nội dung bằng tập lệnh sau:
```
#!/usr/bin/env python  import os print os.environ['VAULT_PASSWORD'] 
```
Làm cho file có thể thực thi bằng lệnh :
```
chmod +x .vault_pass
```
Sau đó, có thể đặt và xuất biến môi trường VAULT_PASSWORD , biến này sẽ có sẵn cho phiên hiện tại :
```
export VAULT_PASSWORD=my_vault_password
```

#### Sử dụng các biến được mã hóa bằng Vault với các biến thông thường

Trong khi Ansible Vault được dùng với các file tùy ý, nó thường được sử dụng để bảo vệ các biến nhạy cảm. Ta sẽ làm việc thông qua một ví dụ để chỉ cho cách chuyển đổi file biến thông thường thành một cấu hình cân bằng giữa bảo mật và khả năng sử dụng.

Giả sử rằng đang cấu hình một server database . Khi tạo file hosts trước đó, đã đặt mục nhập localhost trong một group có tên là database để chuẩn bị cho bước này.

Database thường yêu cầu một hỗn hợp các biến nhạy cảm và không nhạy cảm. Chúng có thể được chỉ định trong folder group_vars trong một file được đặt tên theo group :
```
mkdir -p group_vars 
vi group_vars/database
```

Bên trong group_vars/database , hãy cài đặt một số biến. Một số biến, như số cổng MySQL, không phải là bí mật và có thể được chia sẻ tự do. Các biến khác, như password database , sẽ được bảo mật:

group_vars / database
```
--- # nonsensitive data mysql_port: 3306 mysql_host: 10.0.0.3 mysql_user: fred  # sensitive data mysql_password: supersecretpassword
```
Ta có thể kiểm tra rằng tất cả các biến có sẵn để lưu trữ của ta với Ansible của debug module và hostvars biến:
```
ansible -m debug -a 'var=hostvars[inventory_hostname]' database 
```

Đầu ra xác nhận tất cả các biến ta cài đặt đều được áp dụng cho server . Tuy nhiên, group_vars/database của ta hiện đang chứa tất cả các biến của ta . Điều này nghĩa là ta có thể để nó không được mã hóa, đây là một mối lo ngại về bảo mật do biến password database hoặc ta mã hóa tất cả các biến, điều này tạo ra các vấn đề về khả năng sử dụng và cộng tác.

#### Tham chiếu các biến Vault từ các biến không được mã hóa
```
vi group_vars/database/vars
```
Thêm lại biến mysql_password. Sử dụng Jinja2 templating để tham chiếu đến biến được xác định trong file được bảo vệ bởi vault:
```
group_vars / database / vars
--- # nonsensitive data mysql_port: 3306 mysql_host: 10.0.0.3 mysql_user: fred  # sensitive data mysql_password: "{{ vault_mysql_password }}"
```
Biến mysql_password sẽ được đặt thành giá trị của biến vault_mysql_password , được xác định trong file vault.

Với phương pháp này, có thể hiểu tất cả các biến sẽ được áp dụng cho các server trong group database bằng cách xem group_vars/database/vars . Những phần nhạy cảm sẽ bị che khuất bởi Jinja2 templating. group_vars/database/vault chỉ cần được mở khi bản thân các giá trị cần được xem hoặc thay đổi.

Có thể kiểm tra đảm bảo rằng tất cả các biến mysql_* vẫn được áp dụng chính xác bằng cách sử dụng cùng một phương pháp như lần trước.

Lưu ý: Nếu password Vault không được tự động áp dụng với file password , hãy thêm cờ --ask-vault-pass vào lệnh bên dưới.

ansible -m debug -a 'var=hostvars[inventory_hostname]' database 

Cả vault_mysql_password và mysql_password đều có thể truy cập được. Việc sao chép này là vô hại và sẽ không ảnh hưởng đến việc sử dụng hệ thống này.
