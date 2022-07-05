# Setup iRedMail server with RemoteDB 
Source : https://docs.iredmail.org/install.iredmail.with.remote.mysql.server.html

## 1 . Các thành phần : 
- Mail server : 
    - OS : Ubuntu 20.04 LTS
    - Cấu hình : 4c_8g
    - IP LAN : 10.5.90.134
    - IP WAN : 123.31.11.183 

- Remote Mariadb : 
    - OS : Ubuntu 20.04 LTS
    - Cấu hình : 4c_4g
    - IP LAN : 10.5.90.118

- Domain : tutrangcubeoxam.online

## 2. Lab : 

### Tại node Mariadb :

- Update && Upgrade 
- Install & config secure Mariadb : 
```
apt install -y mariadb-server 
mysql_secure_installation
```
- Config  sql `bind address` listening to all IP  : 
```
--vim /etc/mysql/my.cnf

[mysqld]
bind-address = 0.0.0.0


--systemctl restart mysql
```

- Create sql_user ( để iRedMail remote ) và grant các quyền :
```
-- Run on remote MySQL server as root user

CREATE USER 'root'@'%' IDENTIFIED BY 'password';
GRANT ALL ON *.* TO 'root'@'%' WITH GRANT OPTION; 
FLUSH PRIVILEGES;
FLUSH HOSTS;
```

## tại node Mail server: 
- Update && Upgrade 
- set hostname to `mail.truongvh.com`
```
hostnamectl set-hostname mail.truongvh.com
hostname -f #to verify config
```
- update `vi /etc/hosts` : 
```
127.0.0.1       mail.truongvh.com localhost
```

- Install Mariadb-client & Test connection ( tới SQL server ):
```
apt install mariadb-client-core-10.3 -y

mysql -u admin_iredmail -h 10.5.90.118 -p

```

- Download bản cài,  giải nén  :

```
wget https://github.com/iredmail/iRedMail/archive/1.4.2.tar.gz
tar xvf 1.4.2.tar.gz
cd iRedMail-1.4.2/
```

- Nạp thêm các biến chỉ định remote Mysql, chạy scripts : 
```
USE_EXISTING_MYSQL='YES' \
    MYSQL_SERVER_ADDRESS='10.5.90.118' \
    MYSQL_SERVER_PORT='3306' \
    MYSQL_ROOT_USER='root' \
    MYSQL_ROOT_PASSWD='Truongvh7896@' \
    MYSQL_GRANT_HOST='10.5.90.134'\
    bash iRedMail.sh 
```
- Sau khi cài đặt OK, reboot lại mailserver. 
- Trỏ domain về IP WAN và MX record : 

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/7.png>

- Tại đây, ta đã có thể truy cập vào các site  :  
    - Admin : https://truongvh.com/iredadmin
    - Email : https://truongvh.com/mail/
    - Email ( SOGo interface) : https://truongvh.com/SOGo/

- Tạo 2 user mail tại site admin : 

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/12.png>

- Test gửi, nhận internal : 

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/13.png>

- Test gửi, nhận external to gmail :

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/14.png>

<img src = https://github.com/tulha161/tule/blob/main/iredmail/pic/15.png>


