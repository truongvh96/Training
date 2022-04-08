# Monitoring basic


### 1. [Host và Service](#1)
### 2. [Template](#2)
### 3. [Biến tuỳ chỉnh](#3)
### 4. [Runtime macro](#4)
### 5. [Apply Rules](#5)
### 6. [Groups](#6)
### 7. [Notification](#7)
### 8. [Commands](#8)

<a name=1></a>

# 1. Host và Service

- Icinga2 có thể được sử dụng để theo dõi tính khả dụng của các Host và Service. Host và Service hầu như có thể là bất kỳ thứ gì có thể được kiểm tra theo một cách nào đó:
  <ul>
   <ul>
      <li> Dịch vụ mạng (HTTP, SMTP, SNMP, SSH, v.v.)
      <li> Máy in
      <li> Switch và Router
      <li> Các dịch vụ mạng hoặc cục bộ khác có thể truy cập được
      <li> v.v..
      </ul>
 </ul>
 
 ### Trạng thái của Host
 
  - UP: Host khả dụng
  - DOWN : Host không khả dụng
  
### Trạng thái của Service
  
- OK : Service đang hoạt động tốt
- WARNING : Service đang có 1 số vấn đề nhưng vẫn trong tình trạng làm việc.
- CRITICAL : Service đang trong trạng thái nguy hiểm
- UNKNOW : Không thể kiểm tra trạng thái của Service

###  Trạng thái Hard và Soft

- Khi có vấn đề xảy ra với host/service, Icinga2 sẽ không lập tức gửi cảnh báo đi ngay, mà sẽ tiến hành kiểm tra lại trước khi gửi cảnh báo, nhằm tránh việc những cảnh báo không quan trọng được gửi đi liên tục.
- Trong thời gian check này, object rơi vào trạng thái SOFT
- Sau khi tất cả re-check được tiến hành, và object được check vẫn trong trạng thái non-OK, thì host/service sẽ chuyển sang trạng thái HARD và cảnh báo được gửi đi.
- Để thay đổi thời gian và số lần check tại thư mục `/etc/icinga2/conf.d/templates.conf`.
- Template Icinga2 cung cấp sẵn các template chứa các thông số cơ bản cho host, service, hay việc gửi cảnh báo. Ta cũng có thể tự tạo template để tùy chỉnh theo nhu cầu và import template mới vào các object.


```sh
root@quynv:~# tail -5 /etc/icinga2/conf.d/templates.conf
template Host "Host01" {
  max_check_attempts = 3
  check_interval = 1m
  retry_interval = 30s
}
```

### Kiểm tra host và Service

Đây là các service được icinga cung cấp sẵn để monitor

| Dịch vụ | Áp dụng đến host |
| --- | --- |
|  load, procs, swap, users, icinga | Chỉ host NodeName |
|  ping4, ping6 | Tất cả các host với địa chỉ tương ứng với thuộc tính address6 |
| ssh | Tất cả các host và vars.os đặt là Linux |
| http, optional: Icinga Web 2 | Tất cả các host với tuỳ chọn thuộc tính http_vhosts được định nghĩa trong dictionary |
| disk, disk / | Tất cả các host và tuỳ chọn thuộc tính disks được định nghĩa trong dictionary |



- VD:

```sh
root@quynv:~# tail -5 /etc/icinga2/conf.d/hosts.conf

object Host "local" {
  address = "10.0.0.51"
  check_command = "hostalive"
}
```

```sh
root@quynv:~# tail -5 /etc/icinga2/conf.d/services.conf
object Service "http" {
  host_name = "local"
  check_command = "ping4"
}
```

<a name=2></a>

# 2. Template

  - Template có thể được sử dụng để áp dụng một tập hợp các thuộc tính giống hệt nhau cho nhiều đối tượng:

  ```sh
  root@quynv:~# cat /etc/icinga2/conf.d/templates.conf
  template Service "generic-service" {
  max_check_attempts = 3
  check_interval = 5m
  retry_interval = 1m
  enable_perfdata = true
}

apply Service "ping4" {
  import "generic-service"

  check_command = "ping4"

  assign where host.address
}

apply Service "ping6" {
  import "generic-service"

  check_command = "ping6"

  assign where host.address6
}
```

 - Import template
 
```sh
root@quynv:~# tail -6 /etc/icinga2/conf.d/services.conf
object Service "http" {
  import = "generic-service"
  host_name = "local"
  check_command = "ping4"
}
```
<a name=3></a>
# 3. Biến tuỳ chỉnh

 - Có thể xác định các thuộc tính tuỳ chỉnh bằng cách dùng biến `vars`.

```sh
object Host "localhost" {
  check_command = "ssh"
  vars.ssh_port = 2222
}
```

```
vars["ssh_port"] = 2222
```

```sh
  vars.disks["disk /"] = {
    disk_partitions = "/"
  }
  
```
- Hoặc có thể viết như sau:

```sh
  vars = {
    disks = {
      "disk /" = {
        disk_partitions = "/"
      }
    }
  }
```
- Hàm và biến tuỳ chỉnh

```sh
object CheckCommand "random-value" {
  command = [ PluginDir + "/check_dummy", "0", "$text$" ]

  vars.text = {{ Math.random() * 100 }}
}

object CheckCommand "my-ping" {
  command = [ PluginDir + "/check_ping" ]

  arguments = {
    "-H" = "$ping_address$"
    "-w" = "$ping_wrta$,$ping_wpl$%"
    "-c" = "$ping_crta$,$ping_cpl$%"
    "-p" = "$ping_packets$"
  }

  // Resolve from a host attribute, or custom variable.
  vars.ping_address = "$address$"

  // Default values
  vars.ping_wrta = 100
  vars.ping_wpl = 5

  vars.ping_crta = 250
  vars.ping_cpl = 10

  vars.ping_packets = 5
}

```
<a name=4></a>
# 4. Runtime macro

### Host Runtime Macros

| Name | 	Description |
| -- | -- |
| host.name	| Tên của host object |
| host.display_name |	Giá trị của thuộc tính display_name |
| host.state |	Trạng thái hiện tại của host. Có thể là UNREACHABLE, UP hoặc DOWN |
| host.state_id	| ID trạng thái hiện tại của host. Có thể là một trong 0(UP), 1(DOWN) và 2(UNREACHABLE) |
| host.state_type |	Loại trạng thái hiện tại của host. Có thể là SOFT hoặc HARD |
| host.check_attempt |	Số lần kiểm tra hiện tại |
| host.max_check_attempts	| Số lần kiểm tra tối đa được thực hiện trước khi chuyển sang trạng thái HARD |
| host.last_state	| Trạng thái trước đó của host. Có thể là UNREACHABLE, UP hoặc DOWN |
| host.last_state_id | ID trạng thái trước đó của host. Có thể là 0(UP), 1(DOWN) và 2(ỦNEACHABLE) |
| host.last_state_type | Loại trạng thái trước đó của host. Có thể là SOFT hoặc HARD |
| host.last_state_change |	Dấu thời gian của thay đổi trạng thái cuối cùng |
| host.downtime_depth	| Số lần ngừng hoạt động |
| host.duration_sec |	Thời gian kể từ lần thay đổi trạng thái cuối cùng |
| host.latency	| Độ trễ kiểm tra của host |
| host.execution_time	| Thời gian thực hiện execution của host |
| host.output	| Đầu ra của lần check cuối cùng |
| host.perfdata |	Dữ liệu hiệu suất của lần kiểm tra cuối cùng |
| host.last_check	 | Thời gian khi lần kiểm tra cuối cùng được thực hiện |
| host.check_source |	Phiên bản giám sát thực hiện lần kiểm tra cuối cùng |
| host.num_services	| Số lượng dịch vụ được liên kết với host |
| host.num_services_ok	| Số lượng dịch vụ được liên kết với host đang ở trạng thái OK |
| host.num_services_warning |	Số lượng dịch vụ được liên kết với host đang ở trạng thái WARNING |
| host.num_services_unknown	 | Số lượng dịch vụ được liên kết với host đang ở trạng thái UNKNOWN |
| host.num_services_critical	| Số lượng dịch vụ được liên kết với host đang ở trạng thái CRITICAL |



### Service Runtime Macros

| Tên	| Mô tả |
| -- | -- |
| service.name | Tên của Service. |
| service.display_name	| Giá trị của thuộc tính display_name |
| service.check_command	| Tên lệnh cùng với đối số nào được sử dụng để check |
| service.state	| Trạng thái hiện tại của dịch vụ. Có thể là OK, WARNING, CRITICAL hoặc UNKNOWN |
| service.state_id	| ID trạng thái hiện tại của dịch vụ. Có thể là  0(OK), 1(WARNING), 2(CRITICAL) và 3(UNKNOWN). |
| service.state_type | Loại trạng thái hiện tại của dịch vụ. Có thể là SOFT hoặc HARD. |
| service.check_attempt |	Số lần check hiện tại. |
| service.max_check_attempts |	Số lần check tối đa được thực hiện trước khi chuyển sang trạng thái HARD. |
| service.last_state |	Trạng thái trước đó của dịch vụ. Có thể là OK, WARNING, CRITICAL hoặc UNKNOWN |
| service.last_state_id |	ID trạng thái trước đó của dịch vụ. Có thể là  0(OK), 1(WARNING), 2(CRITICAL) và 3(UNKNOWN). |
| service.last_state_type |	Loại trạng thái trước đó của dịch vụ. Có thể là SOFT hoặc HARD. |
| service.last_state_change |	Thời gian của thay đổi trạng thái cuối cùng. |
| service.downtime_depth |	Số lần ngừng hoạt động. |
| service.duration_sec |	Thời gian kể từ lần thay đổi trạng thái cuối cùng. |
| service.latency	| Độ trễ check của dịch vụ. |
| service.execution_time |	Thời gian thực hiện check của dịch vụ. |
| service.output	| Đầu ra của lần check cuối cùng. |
| service.perfdata	| 	Dữ liệu hiệu suất của lần check cuối cùng. |
| service.last_check	| Thời gian khi lần check cuối cùng được thực hiện. |
| service.check_source	| Phiên bản giám sát thực hiện lần check cuối cùng. |

### Command Runtime Macros

| Tên |	Mô tả
| -- | -- |
| command.name	| Tên của command |

### User Runtime Macros 

| Tên	| Mô tả |
| -- |  --- |
| user.name |	Tên của user |
| user.display_name	| Giá trị của thuộc tính display_name |

### Notification Runtime Macros 

| Tên	| Mô tả |
| -- | -- |
| notification.type	| Kiểu của notification. |
| notification.author	| Author của notification comment. |
| notification.comment	| Comment của notification. |
<a name=5></a>
# 5. Apply Rules


###  Giám sát tất cả các host có host.address được chỉ định

```sh
apply Service "ping4" {
  check_command = "ping4"
  assign where host.address
}
```

### Áp dụng dịch vụ cho host

- Áp dụng dịch vụ cho host có host.address chỉ định và host os là Linux.

```sh
apply Service "ssh" {
  import "generic-service"

  check_command = "ssh"

  assign where host.address && host.vars.os == "Linux"
}

```

### Áp dụng Notifications cho Hosts và Services

- Nitification `mail-noc` sẽ được tạo dưới dạng đối tượng cho tất cả các dịch vụ có biến tuỳ chỉnh `notification.mail`. Lệnh notification được đặt tới `mail-service-notification` và tất cả các user của group `noc` sẽ nhận được thông báo.

```sh
apply Notification "mail-noc" to Service {
  import "mail-service-notification"

  user_groups = [ "noc" ]

  assign where host.vars.notification.mail
}
```

- Template Notification `mail-host-notification` chứa tất cả các cài đặt thông báo có liên quan. Apply rule được áp dụng trên tất cả các host nơi quy tắc `host.address` được xác định.

Nếu host có một tập biến tùy chỉnh cụ thể, giá trị của nó sẽ được kế thừa vào phạm vi đối tượng thông báo cục bộ, ví dụ host.vars.notification_interval: host.vars.notification_periodvà host.vars.notification_type. Điều này ghi đè các thuộc tính đã được chỉ định trong template `mail-host-notification` đã nhập.

```sh
apply Notification "host-mail-noc" to Host {
  import "mail-host-notification"

  // replace interval inherited from `mail-host-notification` template with new notfication interval set by a host custom variable
  if (host.vars.notification_interval) {
    interval = host.vars.notification_interval
  }

  // same with notification period
  if (host.vars.notification_period) {
    period = host.vars.notification_period
  }

  // Send SMS instead of email if the host's custom variable `notification_type` is set to `sms`
  if (host.vars.notification_type == "sms") {
    command = "sms-host-notification"
  } else {
    command = "mail-host-notification"
  }

  user_groups = [ "noc" ]

  assign where host.address
}
```

Host tương ứng

```sh
object Host "host1" {
  import "host-linux-prod"
  display_name = "host1"
  address = "192.168.1.50"
  vars.notification_interval = 1h
  vars.notification_period = "24x7"
  vars.notification_type = "sms"
}
```

<a name=6></a>
# 6. Groups
 - Tập hợp các object giống nhau

```sh
object HostGroup "windows" {
  display_name = "Windows Servers"
}
```

- Thêm host vào group

```sh
template Host "windows-server" {
  groups += [ "windows" ]
}

object Host "mssql-srv1" {
  import "windows-server"

  vars.mssql_port = 1433
}

object Host "mssql-srv2" {
  import "windows-server"

  vars.mssql_port = 1433
}
```

- Có thể áp dụng cho nhóm service và nhóm user

```sh
object UserGroup "windows-mssql-admins" {
  display_name = "Windows MSSQL Admins"
}

template User "generic-windows-mssql-users" {
  groups += [ "windows-mssql-admins" ]
}

object User "win-mssql-noc" {
  import "generic-windows-mssql-users"

  email = "noc@example.com"
}

object User "win-mssql-ops" {
  import "generic-windows-mssql-users"

  email = "ops@example.com"
}
```

### Chỉ định tư cách thành viên nhóm

- Thay vì chỉ định thủ công từng object cho một nhóm, bạn cũng có thể gán các object cho một nhóm dựa trên các thuộc tính của chúng:

```sh
object HostGroup "prod-mssql" {
  display_name = "Production MSSQL Servers"

  assign where host.vars.mssql_port && host.vars.prod_mysql_db
  ignore where host.vars.test_server == true
  ignore where match("*internal", host.name)
}
```

 Tất cả các host có thuộc tính `mssql_port` sẽ được thêm làm thành viên của hostgroup `mssql`. Tuy nhiên, tất cả các host phù hợp với chuỗi thuộc tính `internal` hoặc với `test_server` không được thêm vào nhóm này.
<a name=7></a>
# 7. Notification

- Cảnh báo các vấn đề về service và host là một phần không thể thiếu trong thiết lập giám sát.
- Khi service và host đang gặp sự cố thì thông báo sẽ được gửi đến cho người dùng.
- Có nhiều cách để gửi thông báo, ví dụ như qua email, XMPP, IRC, Twitter, v.v. .Icinga2 không biết cách gửi thông báo. Thay vào đó, nó dựa vào các cơ chế bên ngoài như shell script để thông báo cho người dùng.

```sh
object User "icingaadmin" {
  display_name = "Icinga 2 Admin"
  enable_notifications = true
  states = [ OK, Warning, Critical ]
  types = [ Problem, Recovery ]
  email = "icinga@localhost"
}
```

- User `icingaadmin` trong ví dụ dưới đây sẽ chỉ nhận được cảnh báo về các trạng thái `Warning` và `Critical`. Thêm vào đó các Recovery thông báo được gửi .
- Nếu không set `states` và `types` thì tất cả các thông báo về trạng thái sẽ được gửi.

- Ví dụ một notification template 

```sh
template Notification "generic-notification" {
  interval = 15m

  command = "mail-service-notification"

  states = [ Warning, Critical, Unknown ]
  types = [ Problem, Acknowledgement, Recovery, Custom, FlappingStart,
            FlappingEnd, DowntimeStart, DowntimeEnd, DowntimeRemoved ]

  period = "24x7" //Khoảng thơi gian 24*7
}
```

- Apply keyword tạo notification cho service

```sh
apply Notification "notify-cust-xy-mysql" to Service {
  import "generic-notification"
  users = [ "noc-xy", "mgmt-xy" ]
  assign where match("*has gold support 24x7*", service.notes) && (host.vars.customer == "customer-xy" || host.vars.always_notify == true
  ignore where match("*internal", host.name) || (service.vars.priority < 2 && host.vars.is_clustered == true)
}
```

### User cho host/service

 - Sử dụng các quy tắc `mail` và `sms` cho các đối tượng khác nhau

```sh

object Host "icinga2-agent1.localdomain" {
  [...]

  vars.notification["mail"] = {
    groups = [ "icingaadmins" ]
    users = [ "icingaadmin" ]
  }
  vars.notification["sms"] = {
    users = [ "icingaadmin" ]
  }
}
```

- Sử dụng chi tiết hơn trên service

```sh
apply Service "http" {
  [...]

  vars.notification["mail"] = {
    groups = [ "icingaadmins" ]
    users = [ "icingaadmin" ]
  }

  [...]
}
```
- Dịch vụ thông báo cho user và group được kế thừa từ service và nếu không được thiết lập, từ host. Người dùng mặc định cũng được đặt.


```sh
apply Notification "mail-service-notification" to Service {
  [...]

  if (service.vars.notification.mail.users) {
    users = service.vars.notification.mail.users
  } else if (host.vars.notification.mail.users) {
    users = host.vars.notification.mail.users
  } else {
    /* Default user who receives everything. */
    users = [ "icingaadmin" ]
  }

  if (service.vars.notification.mail.groups) {
    user_groups = service.vars.notification.mail.groups
  } else if (host.vars.notification.mail.groups) {
    user_groups = host.vars.notification.mail.groups
  }

  assign where ( host.vars.notification.mail && typeof(host.vars.notification.mail) == Dictionary ) || ( service.vars.notification.mail && typeof(service.vars.notification.mail) == Dictionary )
}
```

### Độ trễ thông báo và thời gian re-notification

```sh
apply Notification "mail" to Service {
  import "generic-notification"

  command = "mail-notification"
  users = [ "icingaadmin" ]

  interval = 5m  // time renotification
  
  times.begin = 15m // delay notification window

  assign where service.name == "ping4"
}


```
<a name=8></a>

# 8.Commands

- icinga2 sử dụng 3 loại lệnh khác nhau để tiến hành kiểm tra

## CheckCommand

- CheckCommand xác định dòng lệnh chạy như thế nào khi check được gọi.

- Các đối tượng CheckCommand được tham chiếu bởi các đối tượng host và service bằng cách sử dụng thuộc tính `check_command`.

### Tích hợp Plugin với Định nghĩa CheckCommand

- Ta có plugin giám sát check_mysql, sử dụng lệnh help để xem các tuỳ chọn có sẵn


```sh
icinga@icinga2 $ /usr/lib64/nagios/plugins/check_mysql --help
...
This program tests connections to a MySQL server

Usage:
check_mysql [-d database] [-H host] [-P port] [-s socket]
[-u user] [-p password] [-S] [-l] [-a cert] [-k key]
[-C ca-cert] [-D ca-dir] [-L ciphers] [-f optfile] [-g group]
```

- Tạo checkcommand và định nghĩa các tuỳ chọn bằng tham số lệnh

```sh
# vim /etc/icinga2/conf.d/commands.conf

object CheckCommand "my-mysql" {
  command = [ PluginDir + "/check_mysql" ] //constants.conf -> const PluginDir

  arguments = {
    "-H" = "$mysql_host$"
    "-u" = {
      required = true
      value = "$mysql_user$"
    }
    "-p" = "$mysql_password$"
    "-P" = "$mysql_port$"
    "-s" = "$mysql_socket$"
    "-a" = "$mysql_cert$"
    "-d" = "$mysql_database$"
    "-k" = "$mysql_key$"
    "-C" = "$mysql_ca_cert$"
    "-D" = "$mysql_ca_dir$"
    "-L" = "$mysql_ciphers$"
    "-f" = "$mysql_optfile$"
    "-g" = "$mysql_group$"
    "-S" = {
      set_if = "$mysql_check_slave$"
      description = "Check if the slave thread is running properly."
    }
    "-l" = {
      set_if = "$mysql_ssl$"
      description = "Use ssl encryption"
    }
  }

  vars.mysql_check_slave = false
  vars.mysql_ssl = false
  vars.mysql_host = "$address$"
}
```

- Tạo service sử dụng plugin (đảm bảo sử dụng tất cả các tham số bắt buộc, các biến toàn cục bắt buộc là `mysql_user`, `mysql_password`  `mysql_database`,`MysqlUsername` và `MysqlPassword`.)

```sh
# vim /etc/icinga2/conf.d/services.conf

apply Service "mysql-icinga-db-health" {
  import "generic-service"

  check_command = "my-mysql"

  vars.mysql_user = MysqlUsername
  vars.mysql_password = MysqlPassword

  vars.mysql_database = "icinga"
  vars.mysql_host = "192.168.33.11"

  assign where match("icinga2*", host.name)
  ignore where host.vars.no_health_check == true
}
```

## Đối số lệnh

- Để chỉ định các tham số của tuỳ chọn plugins/scripts trong thuộc tính `arguments`
```sh
  arguments = {
    "--parameter" = {
      description = "..."
      value = "..."
    }
  }
 ```
-  Mỗi đối số được bỏ qua khi không được đặt giá trị.

 ### Giá trị
 
 ```sh
   arguments = {
    "--unit" = {
      value = "$systemd_unit$"
    }
  }
  ```
  
 - Để chỉ định giá trị ta dùng 1 biến tuỳ chỉnh bên trong checkcommand

```sh
  vars.systemd_unit = "icinga2"
  ```
  
 ### VALUE
  
  - Cách tốt nhất là luôn sao chép output của tham số lệnh khi sử dụng `help` vào trường `description`
  
  
  ```sh
  ./check_systemd.py --help

...

  -u UNIT, --unit UNIT  Name of the systemd unit that is beeing tested.
  ```
  
 - Sao chép vào description

```sh
 arguments = {
    "--unit" = {
      value = "$systemd_unit$"
      description = "Name of the systemd unit that is beeing tested."
    }
  }
  ```
  
  ### Đối số lệnh bắt buộc
  
 - Chỉ định xem đối số có bắt buộc hay không (Theo mặc định thì tất cả đối số đều là tuỳ chọn).
```sh
  arguments = {
    "--host" = {
      value = "..."
      description = "..."
      required = true  //bắt buộc
    }
  }
  ```
  - Trường required có thể chỉ định bằng một giá trị boolean (True, False).


### Skip key

 - Thuộc tính arguments yêu cầu một key, không được phép để giá trị trống. Để khắc phục điều này cho các tham số không cần tên phía trước giá trị, hãy sử dụng `skip_key` (boolean).
  
  ```sh
    command = [ PrefixDir + "/bin/icingacli", "businessprocess", "process", "check" ]

  arguments = {
    "--process" = {
      value = "$icingacli_businessprocess_process$"
      description = "Business process to monitor"
      skip_key = true
      required = true
      order = -1
    }
  }
  ```
  
- Chỉ định giá chị của biến tuỳ chỉnh "icingacli_businessprocess_process"

  ```sh
    vars.icingacli_businessprocess_process = "bp-shop-web"
```

- Điều này dẫn đến dòng lệnh này thực thi không có tham số "--process"

```sh
'/bin/icingacli' 'businessprocess' 'process' 'check' 'bp-shop-web'
```
  
 - Sử dụng phương pháp này để đưa mọi thứ vào thuộc tính `arguments` theo một thứ tự xác định và không có key. Điều này cũng tránh các mục nhập trong các thuộc tính `command`.
  
  ## Set if
  
  ```sh
    command = [ PluginDir + "/check_http"]

  arguments = {
    "--sni" = {
      set_if = "$http_sni$"
    }
  }
  ```
  
  - Bất cứ khi nào host/service đặt biến tuỳ chỉnh `http_sni` thành true, tham số sẽ được thêm vào dòng lệnh.
  ```sh
  '/usr/lib64/nagios/plugins/check_http' '--sni'
  ```
  
  - Các tham số có giá trị nhưng được kiểm soát bổ sung

```sh
command = [ PluginContribDir + "/check_postgres.pl" ]

  arguments = {
    "-H" = {
      value = "$postgres_host$"
      set_if = {{ macro("$postgres_unixsocket$") == false }}
      description = "hostname(s) to connect to; defaults to none (Unix socket)"
  }
  //Thông số host chỉ sử dụng giá trị value bất cứ khi nào biến tuỳ chỉnh postgres_unixsocket được đặt thành false.
  ```
  
  - Kiểm tra thực thi cho host và service.

```sh
object Host "postgresql-cluster" {
  // ...

  vars.postgres_host = "192.168.56.200"
  vars.postgres_unixsocket = false
}
```

- Lệnh thực thi tương đương

```sh
'/usr/lib64/nagios/plugins/check_postgres.pl'
```

-> Các đối tượng host/service được đặt biến tuỳ chỉnh postgres_unixsocket là false không thêm tham số -H và giá trị của nó vào dòng lệnh.

### Thứ tự

- Plugin có thể yêu cầu các thông số theo thứ tự tuỳ chỉnh.

```sh
  arguments = {
    "--first" = {
      value = "..."
      description = "..."
      order = -5
    }
    "--second" = {
      value = "..."
      description = "..."
      order = -4
    }
    "--last" = {
      value = "..."
      description = "..."
      order = 99
    }
  }
 ```
 
 - Repeat key

- Các tham số có thể sử dụng mảng làm kiểu giá trị. Bất cứ khi nào Icinga gặp một mảng, nó sẽ lặp lại khóa tham số và từng phần tử giá trị theo mặc định.
  
  
  ```sh
    command = [ NscpPath + "\\nscp.exe", "client" ]

  arguments = {
    "-a" = {
      value = "$nscp_arguments$"
      description = "..."
      repeat_key = true
    }
  }
  ```
  
  - Trên một đối tượng host/service, hãy chỉ định biến tuỳ chỉnh nscp_arguments dưới dạng một mảng.
  
```sh
    vars.nscp_arguments = [ "exclude=sppsvc", "exclude=ShellHWDetection" ]
```

- Lệnh thực thi tương đương

```sh
nscp.exe 'client' '-a' 'exclude=sppsvc' '-a' 'exclude=ShellHWDetection'
```

- Nếu không muốn lặp lại khóa, hãy đặt `repeat_key = false`.  

```sh
  command = [ NscpPath + "\\nscp.exe", "client" ]

  arguments = {
    "-a" = {
      value = "$nscp_arguments$"
      description = "..."
      repeat_key = false
    }
  }
  ```
  
  - Lệnh thực thi tương đương

```sh
nscp.exe 'client' '-a' 'exclude=sppsvc' 'exclude=ShellHWDetection'
```
  
### Key

- Thuộc tính arguments yêu cầu các key duy nhất. Nếu muốn ghi đè thuộc tính dòng lệnh hãy sự dụng key với thiết lập các tên khoá khác nhau.
  
  
```sh
  arguments = {
  "--key1" = {
    value = "..."
    key = "-specialkey"
  }
  "--key2" = {
    value = "..."
    key = "-specialkey"
  }
}
```

- Câu lệnh tương đương

```sh
 '-specialkey' '...' '-specialkey' '...'
```

  
 ## Lệnh thông báo
 - Các lệnh thông báo xác định cách thông báo được gửi đến các giao diện bên ngoài (email, XMPP, IRC, Twitter, v.v.). Các lệnh thông báo được tham chiếu bằng cách sử dụng thuộc tính.
  
 ### mail-host-notification
   
 - Đối tượng lệnh thông báo `mail-host-notification` sử dụng tập lệnh thông báo mẫu nằm trong `/etc/icinga2/scripts/mail-host-notification.sh`.

- Các đối số có thể được sử dụng:
  
|Tên	| Mô tả |
| -- | -- |
| notification_date	| (Yêu cầu) Ngày và giờ. Mặc định là $icinga.long_date_time$ |
| notification_hostname	| (Yêu cầu) host FQDN. Mặc định là $host.name$ |
| notification_hostdisplayname	| (Yêu cầu) Tên hiển thị của host. Mặc định là $host.display_name$ |
| notification_hostoutput	| (Yêu cầu) Đầu ra từ kiểm tra host. Mặc định là $host.output$ |
| notification_useremail	| (Yêu cầu) Người nhận thông báo. Mặc định là $user.email$ |
| notification_hoststate	| (Yêu cầu) Tình trạng hiện tại của host. Mặc định là $host.state$ |
| notification_type	| (Yêu cầu) Loại thông báo. Mặc định là $notification.type$ |
| notification_address |	(Không bắt buộc) Địa chỉ IPv4 của host. Mặc định là $address$ |
| notification_address6 |	(Không bắt buộc) Địa chỉ IPv6 của host. Mặc định là $address6$ |
| notification_author	| (Không bắt buộc) author bình luận. Mặc định là $notification.author$.
| notification_comment	| (Không bắt buộc) Comment text. Mặc định là $notification.comment$.
| notification_from	| (Không bắt buộc) Xác định From: hợp lệ (ví dụ "Icinga 2 Host Monitoring <icinga@example.com>"). Yêu cầu GNU mailutils (Debian/Ubuntu) hoặc mailx(RHEL/SUSE) |
| notification_icingaweb2url	| (Không bắt buộc) Xác định URL đến Icinga Web 2 của bạn (ví dụ "https://www.example.com/icingaweb2":) |
| notification_logtosyslog	| (Không bắt buộc) Đặt true để ghi log thông báo vào nhật ký hệ thống; hữu ích cho việc gỡ lỗi. Mặc định là false |
  
  
### mail-service-notification 

- Đối tượng lệnh thông báo `mail-service-notification` sử dụng tập lệnh thông báo mẫu nằm trong `/etc/icinga2/scripts/mail-service-notification.sh`.
- Các đối số có thể được sử dụng:


|Tên	| Mô tả |
| -- | -- |
| notification_date	| (Yêu cầu) Ngày và giờ. Mặc định là $icinga.long_date_time$. |
| notification_hostname	| (	Yêu cầu) Host FQDN. Mặc định là $host.name$. |
| notification_servicename	| (Yêu cầu) Tên dịch vụ. Mặc định là $service.name$. |
| notification_hostdisplayname	| (Yêu cầu) Tên hiển thị máy chủ. Mặc định là $host.display_name$. |
| notification_servicedisplayname	| (Yêu cầu) Tên hiển thị dịch vụ. Mặc định là $service.display_name$. |
| notification_serviceoutput | (Yêu cầu) Đầu ra từ kiểm tra dịch vụ. Mặc định là $service.output$. |
| notification_useremail	| (Yêu cầu)  người nhận thông báo. Mặc định là $user.email$. |
| notification_servicestate |	(Yêu cầu) Tình trạng hiện tại của máy chủ. Mặc định là $service.state$. |
| notification_type |	(Yêu cầu) Loại thông báo. Mặc định là $notification.type$. |
| notification_address |	(Không bắt buộc) Địa chỉ IPv4 của máy chủ. Mặc định là $address$. |
| notification_address6	| (Không bắt buộc) Địa chỉ IPv6 của máy chủ. Mặc định là $address6$. |
| notification_author	| (Không bắt buộc) author bình luận. Mặc định là $notification.author$. |
| notification_comment| (Không bắt buộc) Comment text. Mặc định là $notification.comment$. |
|  notification_from	| (Không bắt buộc) Xác định From: hợp lệ (ví dụ "Icinga 2 Host Monitoring <icinga@example.com>"). Yêu cầu GNU mailutils(Debian/Ubuntu) hoặc mailx(RHEL/SUSE). |
| notification_icingaweb2url	| (Không bắt buộc) Xác định URL đến Icinga Web 2 của bạn (ví dụ "https://www.example.com/icingaweb2":) |
| notification_logtosyslog	| (Không bắt buộc) Đặt true để ghi các log thông báo vào nhật ký hệ thống; hữu ích cho việc gỡ lỗi. Mặc định là false. |

