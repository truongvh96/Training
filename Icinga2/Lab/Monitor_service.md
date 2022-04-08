
# 1. plugin
 
- Tất cả các plugin Icinga hoặc Nagios hiện có đều hoạt động với Icinga2. Có thể tìm thấy các plugin cộng đồng trên Icinga Exchange ...

- Khuyến nghị để thiết lập các plugin này là sao chép chúng vào thư mục `PluginDir`.

### Thiết lập plugin

- Đảm bảo rằng plugin đang hoạt động bình thường bằng cách chạy nó với bất kỳ nào mà Icinga2 đang chạy như:

```sh
sudo -u nagios /usr/lib/nagios/plugins/check_mysql_health --help
 ```
 
 # 2. CheckCommand
 
- Mỗi plugin yêu cầu một object CheckCommand trong cấu hình, object này có thể được sử dụng trong định nghĩa object service hoặc host .

- Ví dụ về kiểm tra kích thước CSDL với check_mysql_health.

```sh
/usr/lib64/nagios/plugins/check_mysql_health --hostname '127.0.0.1' --username root --password icingar0xx --mode sql --name 'select sum(data_length + index_length) / 1024 / 1024 from information_schema.tables where table_schema = '\''icinga'\'';' '--name2' 'db_size' --units 'MB' --warning 4096 --critical 8192
```

 ### Tích hợp file cấu hình icinga
 
 ```sh
 object Host "icinga2-master1.localdomain" {
  check_command = "hostalive"
  address = "..."

  // Database listens locally, not external
  vars.mysql_health_hostname = "127.0.0.1"

  // Basic database size checks for Icinga DBs
  vars.databases["icinga"] = {
    mysql_health_warning = 4096 //MB
    mysql_health_critical = 8192 //MB
  }
  vars.databases["icingaweb2"] = {
    mysql_health_warning = 4096 //MB
    mysql_health_critical = 8192 //MB
  }
}
```

- Host object áp dụng các ngưỡng và quy tắc cho rule. Sử dụng các điều kiện để tìm nạp các giá trị được chỉ định trên host hoặc đặt giá trị mặc định.

```sh
apply Service "db-size-" for (db_name => config in host.vars.databases) {
  check_interval = 1m
  retry_interval = 30s

  check_command = "mysql_health"

  if (config.mysql_health_username) {
    vars.mysql_healt_username = config.mysql_health_username
  } else {
    vars.mysql_health_username = "root"
  }
  if (config.mysql_health_password) {
    vars.mysql_healt_password = config.mysql_health_password
  } else {
    vars.mysql_health_password = "icingar0xx"
  }

  vars.mysql_health_mode = "sql"
  vars.mysql_health_name = "select sum(data_length + index_length) / 1024 / 1024 from information_schema.tables where table_schema = '" + db_name + "';"
  vars.mysql_health_name2 = "db_size"
  vars.mysql_health_units = "MB"

  if (config.mysql_health_warning) {
    vars.mysql_health_warning = config.mysql_health_warning
  }
  if (config.mysql_health_critical) {
    vars.mysql_health_critical = config.mysql_health_critical
  }

  vars += config
}
```


### New Checkcommand

-  Các quy ước sau khi thêm định nghĩa object command mới:
<ul>
 <ul>
<li> Sử dụng các đối số lệnh bất cứ khi nào có thể. Thuộc tính command phải là một mảng [ ... ] để thoát shell.
<li> Xác định một prefix duy nhất cho các đối số cụ thể của lệnh.
 </ul>
 </ul>
```<command name>_<parameter name>```

- Xem mô tả đối số

```sh
./check_systemd.py --help

usage: check_systemd.py [-h] [-c SECONDS] [-e UNIT | -u UNIT] [-v] [-V]
                        [-w SECONDS]

...

optional arguments:
  -h, --help            show this help message and exit
  -c SECONDS, --critical SECONDS
                        Startup time in seconds to result in critical status.
  -e UNIT, --exclude UNIT
                        Exclude a systemd unit from the checks. This option
                        can be applied multiple times. For example: -e mnt-
                        data.mount -e task.service.
  -u UNIT, --unit UNIT  Name of the systemd unit that is beeing tested.
  -v, --verbose         Increase output verbosity (use up to 3 times).
  -V, --version         show program's version number and exit
  -w SECONDS, --warning SECONDS
                        Startup time in seconds to result in warning status.
 ```
 
 
 - CheckCommand không có đối số
```sh
object CheckCommand "systemd" { // Plugin name without 'check_' prefix
  command = [ PluginContribDir + "/check_systemd.py" ] // Use the 'PluginContribDir' constant, see the contributed ITL commands
}
```

- Chạy lệnh xác thực cấu hình để xem có hoạt động không

```sh
icinga2 daemon -C
```


- Thuộc tính `arguments` là một từ điển lấy các tham số làm key.

```sh
 arguments = {
    "--unit" = { ... }
  }
```
- Bản thân giá trị đối số là một từ điển phụ có các key bổ sung:

<ul>
  <ul>
    <li> value : tham chiếu đến chuỗi macro đang chạy.
     <li> description: nơi bạn sao chép văn bản trợ giúp tham số plugin vào
     <li> required, set_if .v.. : để biết các tham số nâng cao.
  </ul>
  </ul>

  
  
# 3. API plugin

### Ouput

- Càng ngắn gọn càng dễ hiểu càng tốt

```sh
<STATUS>: <A short description what happened>

OK: MySQL connection time is fine (0.0002s)
WARNING: MySQL connection time is slow (0.5s > 0.1s threshold)
CRITICAL: MySQL connection time is causing degraded performance (3s > 0.5s threshold)
```

### Trạng thái dịch vụ

| Giá trị	| Trạng thái	| Mô tả |
| - | -- | -- |
| 0	| OK |	Mọi thứ hoạt động bình thường |
| 1	| Warning |	 Vượt quá ngưỡng cảnh báo |
| 2	| Critical |	Vượt quá ngưỡng nghiêm trọng hoặc bị hỏng |
| 3	| Unknown |	Tham số không hợp lệ , lỗi resource mức thấp (thiết bị IO bận, TCP Socket, v.v.) |

### Ngưỡng

- Trạng thái của dịch vụ quyết định ở một giá trị ngưỡng.

- VD:

```sh
ptc_value = 57.8

warning = 50
critical = 60
```
-> ptc_value đang ở mức cảnh báo

- Thứ tự đánh giá ngưỡng

<ul>
 <ul>
  <li> Ngưỡng Critical được đánh giá đầu tiên
   <li> Ngưỡng Warning được đánh giá thứ hai
    <li> Nếu không có ngưỡng nào khớp, return trạng thái OK
 </ul>
 </ul>
