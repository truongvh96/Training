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
Bạn sẽ được yêu cầu nhập và xác nhận password.

Để kiểm tra chức năng mã hóa, hãy nhập một số văn bản kiểm tra:

Secret information 

Ansible sẽ mã hóa nội dung khi bạn đóng file . Nếu bạn kiểm tra file , thay vì nhìn thấy các từ bạn đã nhập, bạn sẽ thấy một khối được mã hóa:

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
Như bạn thấy , Ansible mã hóa nội dung hiện có giống như cách nó mã hóa các file mới.

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

Cách đơn giản nhất để giải mã nội dung trong thời gian chạy là Ansible nhắc bạn về thông tin đăng nhập thích hợp. Bạn có thể thực hiện việc này bằng cách thêm --ask-vault-pass vào bất kỳ ansible hoặc ansible-playbook . Ansible sẽ nhắc bạn nhập password mà nó sẽ sử dụng để cố gắng giải mã mọi nội dung được bảo vệ bởi vault mà nó tìm thấy.

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
Đến đây bạn có thể thực thi lệnh mà không có --vault-password-file cho phiên hiện tại:
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

Trong khi Ansible Vault được dùng với các file tùy ý, nó thường được sử dụng để bảo vệ các biến nhạy cảm. Ta sẽ làm việc thông qua một ví dụ để chỉ cho bạn cách chuyển đổi file biến thông thường thành một cấu hình cân bằng giữa bảo mật và khả năng sử dụng.

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

#### Di chuyển các biến nhạy cảm vào Ansible Vault

Để giải quyết vấn đề này, ta cần phải phân biệt giữa các biến nhạy cảm và không nhạy cảm. Ta sẽ có thể mã hóa các giá trị bí mật và đồng thời dễ dàng chia sẻ các biến vô nghĩa của ta . Để làm như vậy, ta sẽ chia các biến của ta giữa hai file .

Có thể sử dụng thư mục biến thay cho tệp biến Ansible để áp dụng các biến từ nhiều file . Ta có thể tái cấu trúc để tận dụng khả năng đó. Đầu tiên, đổi tên file hiện có từ database thành vars . Đây sẽ là file biến không được mã hóa của ta :

mv group_vars/database group_vars/vars 
Tiếp theo, tạo một folder có cùng tên với file biến cũ. Di chuyển file vars vào bên trong:

mkdir group_vars/database 
mv group_vars/vars group_vars/database/ 
Bây giờ ta có một folder biến cho group database thay vì một file duy nhất và ta có một file biến duy nhất không được mã hóa. Vì ta sẽ mã hóa các biến nhạy cảm của bạn , ta nên xóa chúng khỏi file không được mã hóa của bạn . Chỉnh sửa group_vars/database/vars để xóa dữ liệu bí mật:

nano group_vars/database/vars 
Trong trường hợp này, ta muốn xóa biến mysql_password . Tệp bây giờ sẽ trông như thế này:

group_vars / database / vars
--- # nonsensitive data mysql_port: 3306 mysql_host: 10.0.0.3 mysql_user: fred 
Tiếp theo, tạo một file được mã hóa vault trong folder sẽ nằm cùng với file vars không được mã hóa:

ansible-vault create group_vars/database/vault 
Trong file này, xác định các biến nhạy cảm đã từng có trong file vars . Sử dụng các tên biến giống nhau, nhưng thêm vào trước chuỗi vault_ để cho biết các biến này được xác định trong file được bảo vệ bởi vault:

group_vars / database / vault
--- vault_mysql_password: supersecretpassword 
Lưu file khi bạn hoàn tất.

Cấu trúc folder kết quả trông như sau:

. ├── . . . ├── group_vars/ │   └── database/ │       ├── vars │       └── vault └── . . . 
Đến đây, các biến là riêng biệt và chỉ có dữ liệu bí mật được mã hóa. Điều này là an toàn, nhưng việc triển khai của ta đã ảnh hưởng đến khả năng sử dụng của ta . Mặc dù mục tiêu của ta là bảo vệ các giá trị nhạy cảm, ta cũng đã vô tình làm giảm khả năng hiển thị đối với các tên biến thực tế. Không rõ biến nào được gán mà không tham chiếu đến nhiều file và trong khi bạn có thể cần hạn chế quyền truy cập vào dữ liệu bí mật trong khi cộng tác, bạn vẫn có thể cần chia sẻ tên biến.

Để giải quyết vấn đề này, dự án Ansible thường đề xuất một cách tiếp cận hơi khác.

Tham chiếu các biến Vault từ các biến không được mã hóa
Khi ta di chuyển dữ liệu nhạy cảm của bạn sang file được bảo vệ bởi vault, ta đã đặt trước tên biến bằng vault_ ( mysql_password trở thành vault_mysql_password ). Ta có thể thêm các tên biến ban đầu ( mysql_password ) trở lại file không được mã hóa. Thay vì đặt chúng thành các giá trị nhạy cảm trực tiếp, ta có thể sử dụng các câu lệnh tạo mẫu Jinja2 để tham chiếu các tên biến được mã hóa từ bên trong file biến không được mã hóa của ta . Bằng cách này, bạn có thể xem tất cả các biến đã xác định bằng cách tham chiếu đến một file duy nhất, nhưng các giá trị bí mật sẽ vẫn còn trong file được mã hóa.

Để chứng minh, hãy mở lại file biến không được mã hóa:

nano group_vars/database/vars 
Thêm lại biến mysql_password . Lần này, sử dụng Jinja2 templating để tham chiếu đến biến được xác định trong file được bảo vệ bởi vault:

group_vars / database / vars
--- # nonsensitive data mysql_port: 3306 mysql_host: 10.0.0.3 mysql_user: fred  # sensitive data mysql_password: "{{ vault_mysql_password }}" 
Biến mysql_password sẽ được đặt thành giá trị của biến vault_mysql_password , được xác định trong file vault.

Với phương pháp này, bạn có thể hiểu tất cả các biến sẽ được áp dụng cho các server trong group database bằng cách xem group_vars/database/vars . Những phần nhạy cảm sẽ bị che khuất bởi Jinja2 templating. group_vars/database/vault chỉ cần được mở khi bản thân các giá trị cần được xem hoặc thay đổi.

Bạn có thể kiểm tra đảm bảo rằng tất cả các biến mysql_* vẫn được áp dụng chính xác bằng cách sử dụng cùng một phương pháp như lần trước.

Lưu ý: Nếu password Vault của bạn không được tự động áp dụng với file password , hãy thêm cờ --ask-vault-pass vào lệnh bên dưới.

ansible -m debug -a 'var=hostvars[inventory_hostname]' database 
Output
localhost | SUCCESS => {     "hostvars[inventory_hostname]": {         "ansible_check_mode": false,          "ansible_version": {             "full": "2.2.0.0",              "major": 2,              "minor": 2,              "revision": 0,              "string": "2.2.0.0"         },          "group_names": [             "database"         ],          "groups": {             "all": [                 "localhost"             ],              "database": [                 "localhost"             ],              "ungrouped": []         },          "inventory_dir": "/home/sammy/vault",          "inventory_file": "./hosts",          "inventory_hostname": "localhost",          "inventory_hostname_short": "localhost",          "mysql_host": "10.0.0.3",         "mysql_password": "supersecretpassword",         "mysql_port": 3306,         "mysql_user": "fred",         "omit": "__omit_place_holder__6dd15dda7eddafe98b6226226c7298934f666fc8",          "playbook_dir": ".",          "vault_mysql_password": "supersecretpassword"     } } 
Cả vault_mysql_password và mysql_password đều có thể truy cập được. Việc sao chép này là vô hại và sẽ không ảnh hưởng đến việc bạn sử dụng hệ thống này.
