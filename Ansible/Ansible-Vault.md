Ansible Vault là gì?
Vault là một cơ chế cho phép nội dung được mã hóa được kết hợp một cách minh bạch vào quy trình làm việc Ansible. Một tiện ích có tên ansible-vault bảo mật dữ liệu bí mật bằng cách mã hóa nó trên đĩa. Để tích hợp những bí mật này với dữ liệu Ansible thông thường, cả lệnh ansible và ansible-playbook , để thực hiện các việc đặc biệt và playbook có cấu trúc, đều có hỗ trợ giải mã nội dung được mã hóa vault trong thời gian chạy.

Vault được triển khai với mức độ chi tiết ở cấp độ file , nghĩa là các file được mã hóa hoàn toàn hoặc không được mã hóa. Nó sử dụng thuật toán AES256 để cung cấp mã hóa đối xứng được khóa cho password do user cung cấp. Điều này nghĩa là cùng một password được sử dụng để mã hóa và giải mã nội dung, điều này rất hữu ích từ quan điểm khả năng sử dụng. Ansible có thể xác định và giải mã các file nào được mã hóa vault mà nó tìm thấy trong khi thực hiện một playbook hoặc tác vụ.

Mặc dù có những đề xuất để thay đổi điều này , tại thời điểm viết bài này, user chỉ có thể chuyển một password duy nhất cho Ansible. Điều này nghĩa là mỗi file được mã hóa liên quan phải dùng chung một password .

Đến đây bạn đã hiểu một chút về Vault là gì, ta có thể bắt đầu thảo luận về các công cụ mà Ansible cung cấp và cách sử dụng Vault với các quy trình công việc hiện có.

Đặt Ansible Vault Editor
Trước khi sử dụng lệnh ansible-vault , bạn nên chỉ định editor ưa thích của bạn . Một số lệnh của Vault liên quan đến việc mở editor để thao tác nội dung của file được mã hóa. Ansible sẽ xem xét biến môi trường EDITOR để tìm trình soạn thảo bạn muốn . Nếu điều này không được đặt, nó sẽ mặc định là vi .

Nếu bạn không muốn chỉnh sửa với trình soạn thảo vi , bạn nên đặt biến EDITOR trong môi trường của bạn.

Lưu ý: Nếu bạn vô tình thấy mình đang ở trong một phiên vi , bạn có thể thoát bằng cách nhấn phím Esc , nhập :q! , rồi nhấn Enter . Nếu bạn không quen với trình soạn thảo vi , bất kỳ thay đổi nào bạn thực hiện có thể là do vô ý, vì vậy lệnh này sẽ thoát mà không lưu.

Để đặt trình soạn thảo cho một lệnh riêng lẻ, hãy thêm trước lệnh đó với phép gán biến môi trường, như sau:

EDITOR=nano ansible-vault . . . 
Để làm cho điều này liên tục, hãy mở file ~/.bashrc của bạn:

nano ~/.bashrc 
Chỉ định trình soạn thảo bạn muốn bằng cách thêm bài tập EDITOR vào cuối file :

~ / .bashrc
export EDITOR=nano 
Lưu file khi bạn hoàn tất.

Nguồn lại file để đọc thay đổi trong phiên hiện tại:

. ~/.bashrc 
Hiển thị biến EDITOR để kiểm tra xem cài đặt của bạn đã được áp dụng chưa:

echo $EDITOR 
Output
nano 
Đến đây bạn đã cài đặt trình soạn thảo ưa thích của bạn , ta có thể thảo luận về các hoạt động có sẵn với lệnh ansible-vault .

Cách quản lý file nhạy cảm với ansible-vault
Lệnh ansible-vault là giao diện chính để quản lý nội dung được mã hóa trong Ansible. Lệnh này được sử dụng để mã hóa ban đầu các file và sau đó được sử dụng để xem, chỉnh sửa hoặc giải mã dữ liệu.

Tạo file được mã hóa mới
Để tạo một file mới được mã hóa bằng Vault, hãy sử dụng lệnh ansible-vault create . Nhập tên của file bạn muốn tạo. Ví dụ: để tạo file YAML được mã hóa có tên là vault.yml để lưu trữ các biến nhạy cảm, có thể chạy lệnh:

ansible-vault create vault.yml 
Bạn sẽ được yêu cầu nhập và xác nhận password :

Output
New Vault password:  Confirm New Vault password: 
Khi bạn đã xác nhận password của bạn , Ansible sẽ ngay lập tức mở một cửa sổ chỉnh sửa, nơi bạn có thể nhập nội dung mong muốn của bạn .

Để kiểm tra chức năng mã hóa, hãy nhập một số văn bản kiểm tra:

vault.yml
Secret information 
Ansible sẽ mã hóa nội dung khi bạn đóng file . Nếu bạn kiểm tra file , thay vì nhìn thấy các từ bạn đã nhập, bạn sẽ thấy một khối được mã hóa:

cat vault.yml 
Output
$ANSIBLE_VAULT;1.1;AES256 65316332393532313030636134643235316439336133363531303838376235376635373430336333 3963353630373161356638376361646338353763363434360a363138376163666265336433633664 30336233323664306434626363643731626536643833336638356661396364313666366231616261 3764656365313263620a383666383233626665376364323062393462373266663066366536306163 31643731343666353761633563633634326139396230313734333034653238303166 
Ta có thể thấy một số thông tin tiêu đề mà Ansible sử dụng để biết cách xử lý file , tiếp theo là nội dung được mã hóa, hiển thị dưới dạng số.

Mã hóa các file hiện có
Nếu bạn đã có một file mà bạn muốn mã hóa bằng Vault, hãy sử dụng lệnh ansible-vault encrypt để thay thế.

Để thử nghiệm, ta có thể tạo một file ví dụ bằng lệnh :

echo 'unencrypted stuff' > encrypt_me.txt 
Bây giờ, bạn có thể mã hóa file hiện có bằng lệnh :

ansible-vault encrypt encrypt_me.txt 
, bạn sẽ được yêu cầu cung cấp và xác nhận password . Sau đó, một thông báo sẽ xác nhận mã hóa:

Output
New Vault password:  Confirm New Vault password: Encryption successful 
Thay vì mở cửa sổ chỉnh sửa, ansible-vault sẽ mã hóa nội dung của file và ghi nó trở lại đĩa, thay thế version không được mã hóa.

Nếu ta kiểm tra file , ta sẽ thấy một mẫu mã hóa tương tự:

cat encrypt_me.txt 
Output
$ANSIBLE_VAULT;1.1;AES256 66633936653834616130346436353865303665396430383430353366616263323161393639393136 3737316539353434666438373035653132383434303338640a396635313062386464306132313834 34313336313338623537333332356231386438666565616537616538653465333431306638643961 3636663633363562320a613661313966376361396336383864656632376134353039663662666437 39393639343966363565636161316339643033393132626639303332373339376664 
Như bạn thấy , Ansible mã hóa nội dung hiện có giống như cách nó mã hóa các file mới.

Xem các file được mã hóa
Đôi khi, bạn có thể cần phải tham chiếu đến nội dung của file được mã hóa vault mà không cần chỉnh sửa hoặc ghi nó vào hệ thống file không được mã hóa. Lệnh ansible-vault view cung cấp nội dung của file để chuẩn hóa. Theo mặc định, điều này nghĩa là nội dung được hiển thị trong terminal .

Chuyển file được mã hóa vault vào lệnh:

ansible-vault view vault.yml 
Bạn cần nhập password của file . Sau khi nhập thành công, nội dung sẽ được hiển thị:

Output
Vault password: Secret information 
Như bạn thấy , dấu nhắc password được trộn vào kết quả của nội dung file . Hãy nhớ điều này khi sử dụng ansible-vault view trong các quy trình tự động.

Chỉnh sửa file được mã hóa
Khi bạn cần chỉnh sửa file được mã hóa, hãy sử dụng lệnh ansible-vault edit :

ansible-vault edit vault.yml 
Bạn sẽ được yêu cầu nhập password của file . Sau khi nhập nó, Ansible sẽ mở file một cửa sổ chỉnh sửa, nơi bạn có thể thực hiện bất kỳ thay đổi cần thiết nào.

Sau khi lưu, nội dung mới sẽ được mã hóa lại bằng password mã hóa của file và được ghi vào đĩa.

Giải mã thủ công các file được mã hóa
Để giải mã file được mã hóa vault, hãy sử dụng lệnh ansible-vault decrypt .

Lưu ý: Do khả năng vô tình đưa dữ liệu nhạy cảm vào kho dự án của bạn tăng lên, lệnh ansible-vault decrypt chỉ được đề xuất khi bạn muốn xóa mã hóa vĩnh viễn khỏi file . Nếu bạn cần xem hoặc chỉnh sửa file được mã hóa vault, thường tốt hơn là sử dụng ansible-vault view ansible-vault edit hoặc lệnh ansible-vault edit tương ứng.

Nhập tên của file được mã hóa:

ansible-vault decrypt vault.yml 
Bạn sẽ được yêu cầu nhập password mã hóa cho file . Sau khi bạn nhập đúng password , file sẽ được giải mã:

Output
Vault password: Decryption successful 
Nếu bạn xem lại file , thay vì mã hóa vault, bạn sẽ thấy nội dung thực của file :

cat vault.yml 
Output
Secret information 
Tệp của bạn bây giờ không được mã hóa trên đĩa. Đảm bảo xóa mọi thông tin nhạy cảm hoặc mã hóa lại file khi bạn hoàn tất.

Thay đổi password của các file được mã hóa
Nếu bạn cần thay đổi password của file được mã hóa, hãy sử dụng lệnh ansible-vault rekey :

ansible-vault rekey encrypt_me.txt 
Khi bạn nhập lệnh, trước tiên bạn sẽ được yêu cầu với password hiện tại của file :

Output
Vault password: 
Sau khi nhập nó, bạn cần chọn và xác nhận password vault mới:

Output
Vault password: New Vault password: Confirm New Vault password: 
Khi bạn đã xác nhận thành công password mới, bạn sẽ nhận được thông báo cho biết quá trình mã hóa lại thành công:

Output
Rekey successful 
Bây giờ có thể truy cập file bằng password mới. Mật khẩu cũ sẽ không hoạt động nữa.

Chạy Ansible với các file được mã hóa Vault
Sau khi mã hóa thông tin nhạy cảm của bạn bằng Vault, bạn có thể bắt đầu sử dụng các file bằng công cụ thông thường của Ansible. Cả hai lệnh ansible và ansible-playbook đều biết cách giải mã các file được bảo vệ bởi vault được cung cấp đúng password . Có một số cách khác nhau để cung cấp password cho các lệnh này tùy thuộc vào nhu cầu của bạn.

Để làm theo, bạn cần một file được mã hóa vault. Bạn có thể tạo một cái bằng lệnh :

ansible-vault create secret_key 
Chọn và xác nhận password . Điền vào bất kỳ nội dung giả nào bạn muốn:

chìa khoá bí mật
confidential data 
Lưu và đóng file .

Ta cũng có thể tạo file hosts tạm thời làm khoảng không quảng cáo:

nano hosts 
Ta sẽ chỉ thêm localhost Ansible vào nó. Để chuẩn bị cho bước sau, ta sẽ đặt nó vào group [database] :

server
[database] localhost ansible_connection=local 
Lưu file khi bạn hoàn tất.

Tiếp theo, tạo file ansible.cfg trong folder hiện tại nếu file chưa tồn tại:

nano ansible.cfg 
Hiện tại, chỉ cần thêm phần [defaults] và trỏ Ansible vào khoảng không quảng cáo mà ta vừa tạo:

ansible.cfg
[defaults] inventory = ./hosts 
Khi đã sẵn sàng , hãy tiếp tục.

Sử dụng dấu nhắc tương tác
Cách đơn giản nhất để giải mã nội dung trong thời gian chạy là Ansible nhắc bạn về thông tin đăng nhập thích hợp. Bạn có thể thực hiện việc này bằng cách thêm --ask-vault-pass vào bất kỳ ansible hoặc ansible-playbook . Ansible sẽ nhắc bạn nhập password mà nó sẽ sử dụng để cố gắng giải mã mọi nội dung được bảo vệ bởi vault mà nó tìm thấy.

Ví dụ: nếu ta cần sao chép nội dung của file được mã hóa vault sang server lưu trữ, ta có thể làm như vậy với module copy và cờ --ask-vault-pass . Nếu file thực sự chứa dữ liệu nhạy cảm, rất có thể bạn cần khóa quyền truy cập trên server từ xa với các hạn chế về quyền và quyền sở hữu.

Lưu ý: Ta đang sử dụng localhost làm server đích trong ví dụ này để giảm thiểu số lượng server được yêu cầu, nhưng kết quả sẽ giống như nếu server thực sự ở xa:

ansible --ask-vault-pass -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost 
Nhiệm vụ của ta chỉ định rằng quyền sở hữu của file phải được thay đổi thành root , do đó, quyền quản trị là bắt buộc. Cờ -bK yêu cầu Ansible nhắc nhập password sudo cho server đích, vì vậy bạn cần nhập password sudo của bạn . Sau đó, bạn cần nhập password Vault:

Output
SUDO password: Vault password: 
Khi password được cung cấp, Ansible sẽ cố gắng thực thi tác vụ, sử dụng password Vault cho các file được mã hóa nào mà nó tìm thấy. Lưu ý tất cả các file được tham chiếu trong quá trình thực thi phải sử dụng cùng một password :

Output
localhost | SUCCESS => {     "changed": true,      "checksum": "7a2eb5528c44877da9b0250710cba321bc6dac2d",      "dest": "/tmp/secret_key",      "gid": 0,      "group": "root",      "md5sum": "270ac7da333dd1db7d5f7d8307bd6b41",      "mode": "0600",      "owner": "root",      "size": 18,      "src": "/home/sammy/.ansible/tmp/ansible-tmp-1480978964.81-196645606972905/source",      "state": "file",      "uid": 0 } 
Nhắc password là an toàn, nhưng có thể tẻ nhạt, đặc biệt là khi chạy lặp lại và cũng cản trở quá trình tự động hóa. Rất may, có một số lựa chọn thay thế cho những tình huống này.

Sử dụng Ansible Vault với file password
Nếu bạn không muốn nhập password Vault mỗi khi thực thi một tác vụ, bạn có thể thêm password Vault của bạn vào một file và tham chiếu file trong quá trình thực thi.

Ví dụ: bạn có thể đặt password của bạn trong file .vault_pass như sau:

echo 'my_vault_password' > .vault_pass 
Nếu bạn đang sử dụng kiểm soát version , hãy đảm bảo thêm file password vào file bỏ qua của phần mềm kiểm soát version của bạn để tránh vô tình phạm phải:

echo '.vault_pass' >> .gitignore 
Bây giờ, bạn có thể tham chiếu file thay thế. --vault-password-file có sẵn trên dòng lệnh. Ta có thể hoàn thành nhiệm vụ tương tự từ phần cuối cùng bằng lệnh :

ansible --vault-password-file=.vault_pass -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost 
Bạn sẽ không được yêu cầu nhập password Vault lần này.

Tự động đọc file password
Để tránh phải cung cấp cờ, bạn có thể đặt biến môi trường ANSIBLE_VAULT_PASSWORD_FILE với đường dẫn đến file password :

export ANSIBLE_VAULT_PASSWORD_FILE=./.vault_pass 
Đến đây bạn có thể thực thi lệnh mà không có --vault-password-file cho phiên hiện tại:

ansible -bK -m copy -a 'src=secret_key dest=/tmp/secret_key mode=0600 owner=root group=root' localhost 
Để làm cho Ansible biết vị trí file password qua các phiên, bạn có thể chỉnh sửa file ansible.cfg của bạn .

Mở file ansible.cfg local mà ta đã tạo trước đó:

nano ansible.cfg 
Trong phần [defaults] , hãy đặt cài đặt vault_password_file . Trỏ đến vị trí của file password của bạn. Đây có thể là một đường dẫn tương đối hoặc tuyệt đối, tùy thuộc vào đường dẫn nào hữu ích nhất cho bạn:

ansible.cfg
[defaults] . . . vault_password_file = ./.vault_pass 
Bây giờ, khi bạn chạy các lệnh yêu cầu giải mã, bạn sẽ không còn được yêu cầu nhập password vault nữa. Như một phần thưởng, ansible-vault sẽ không chỉ sử dụng password trong file để giải mã các file nào, mà nó sẽ áp dụng password khi tạo file mới với ansible-vault create và ansible-vault encrypt .

Đọc password từ một biến môi trường
Bạn có thể lo lắng về việc vô tình chuyển file password của bạn vào repository của bạn. Thật không may, trong khi Ansible có một biến môi trường để trỏ đến vị trí của file password , nó không có biến môi trường để tự đặt password .

Tuy nhiên, nếu file password của bạn có thể thực thi, Ansible sẽ chạy nó dưới dạng tập lệnh và sử dụng kết quả kết quả làm password . Trong một vấn đề GitHub , Brian Schwind đề xuất có thể sử dụng tập lệnh sau để lấy password từ một biến môi trường.

Mở file .vault_pass trong editor :

nano .vault_pass 
Thay thế nội dung bằng tập lệnh sau:

.vault_pass
#!/usr/bin/env python  import os print os.environ['VAULT_PASSWORD'] 
Làm cho file có thể thực thi bằng lệnh :

chmod +x .vault_pass 
Sau đó, bạn có thể đặt và xuất biến môi trường VAULT_PASSWORD , biến này sẽ có sẵn cho phiên hiện tại của bạn:

export VAULT_PASSWORD=my_vault_password 
Bạn sẽ phải làm điều này vào đầu mỗi phiên Ansible, điều này nghe có vẻ bất tiện. Tuy nhiên, điều này bảo vệ hiệu quả khỏi việc vô tình sử dụng password mã hóa Vault của bạn, điều này có thể có những nhược điểm nghiêm trọng.

Sử dụng các biến được mã hóa bằng Vault với các biến thông thường
Trong khi Ansible Vault được dùng với các file tùy ý, nó thường được sử dụng để bảo vệ các biến nhạy cảm. Ta sẽ làm việc thông qua một ví dụ để chỉ cho bạn cách chuyển đổi file biến thông thường thành một cấu hình cân bằng giữa bảo mật và khả năng sử dụng.

Cài đặt ví dụ
Giả sử rằng bạn đang cấu hình một server database . Khi bạn tạo file hosts trước đó, bạn đã đặt mục nhập localhost trong một group có tên là database để chuẩn bị cho bước này.

Database thường yêu cầu một hỗn hợp các biến nhạy cảm và không nhạy cảm. Chúng có thể được chỉ định trong folder group_vars trong một file được đặt tên theo group :

mkdir -p group_vars 
nano group_vars/database 
Bên trong group_vars/database , hãy cài đặt một số biến. Một số biến, như số cổng MySQL, không phải là bí mật và có thể được chia sẻ tự do. Các biến khác, như password database , sẽ được bảo mật:

group_vars / database
--- # nonsensitive data mysql_port: 3306 mysql_host: 10.0.0.3 mysql_user: fred  # sensitive data mysql_password: supersecretpassword 
Ta có thể kiểm tra rằng tất cả các biến có sẵn để lưu trữ của ta với Ansible của debug module và hostvars biến:

ansible -m debug -a 'var=hostvars[inventory_hostname]' database 
Output
localhost | SUCCESS => {     "hostvars[inventory_hostname]": {         "ansible_check_mode": false,          "ansible_version": {             "full": "2.2.0.0",              "major": 2,              "minor": 2,              "revision": 0,              "string": "2.2.0.0"         },          "group_names": [             "database"         ],          "groups": {             "all": [                 "localhost"             ],              "database": [                 "localhost"             ],              "ungrouped": []         },          "inventory_dir": "/home/sammy",          "inventory_file": "hosts",          "inventory_hostname": "localhost",          "inventory_hostname_short": "localhost",          "mysql_host": "10.0.0.3",         "mysql_password": "supersecretpassword",         "mysql_port": 3306,         "mysql_user": "fred",         "omit": "__omit_place_holder__1c934a5a224ca1d235ff05eb9bda22044a6fb400",          "playbook_dir": "."     } } 
Đầu ra xác nhận tất cả các biến ta cài đặt đều được áp dụng cho server . Tuy nhiên, group_vars/database của ta hiện đang chứa tất cả các biến của ta . Điều này nghĩa là ta có thể để nó không được mã hóa, đây là một mối lo ngại về bảo mật do biến password database hoặc ta mã hóa tất cả các biến, điều này tạo ra các vấn đề về khả năng sử dụng và cộng tác.

Di chuyển các biến nhạy cảm vào Ansible Vault
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
