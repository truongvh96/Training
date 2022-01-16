Cài đặt VirtualBox 6.0 trên Ubuntu 18.04
VirtualBox là một phần mềm ảo hóa mã nguồn mở có thể cài đặt trên đa nền tảng cho phép chạy đồng thời nhiều Guest OS (Virtual Machine).

1. Import GPG Key của Repo Oracle Virtualbox vào hệ thống :
```
$ sudo wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add - $ sudo wget -q https://www.virtualbox.org/download/oracle_vbox.asc -O- | sudo apt-key add -
```
2. Add VirtualBox APT Repository dùng lệnh add-apt-repository
```
$ sudo add-apt-repository "deb [arch=amd64] http://download.virtualbox.org/virtualbox/debian $(lsb_release -cs) contrib"
```
3. Update new package & install VirtualBox 6.0
```
$ sudo apt update $ sudo apt install virtualbox-6.0
```
4. Khởi động VirtualBox 6.0
```
$ virtualbox
```
