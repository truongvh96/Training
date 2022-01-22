## Giới thiệu

Libvirt là một bộ các phần mềm mà cung cấp các cách thuận tiện để quản lý máy ảo và các chức năng của ảo hóa. Những phần mềm này bao gồm một thư viện API daemon (libvirtd) và các gói tiện tích giao diện dòng lệnh (virsh).
Mục đích chính của Libvirt là cung cấp một cách duy nhất để quản lý ảo hóa từ các nhà cung cấp và các loại hypervisor khác nhau. Ví dụ, dòng lệnh virsh list có thể được sử dụng để liệt kê ra các máy ảo đang tồn tại cho một số hypervisor được hỗ trợ (KVM, Xen, Vmware ESX, … ). Không cần thiết phải sử dụng một tool xác định cho từng hypervisor.

**1. Create VM from image**
```
virt-install \
--virt-type=kvm \
--os-type=Linux \
--name centos7 \
--ram 1024 \
--vcpus=1 \
--os-variant=centos7.0 \
--hvm \
--extra-args "console=ttyS0" \
--location=/var/lib/libvirt/boot/CentOS-7-x86_64-Minimal-2009.iso \
--network=bridge=virbr0,model=virtio \
--disk path=/var/lib/libvirt/images/centos7.qcow2,size=10,bus=virtio,format=qcow2
```
- `--virt-type:` chọn loại ảo hóa
- `--os-type:` Loại os
- `--name`: tên vm
- `--vcpu`: số vcpu cấp cho vm
- `--os-variant`: chọn biến thế của os, để lấy giá trị osinfo-query os
- `--hvm`: sử dụng full virtualization
- `--extra-args`: set accept console từ cli
- `--location`: Chọn nơi chứa image cài đặt (iso, qcow2,...)
- `--network`: thêm nic cho vm
- `--disk path`: hard disk cho vm

**2. VM **
- `virsh start vm`: start vm
- `virsh autostart vm`: cho vm tự khởi động cùng host.
- `virsh suspend vm`: pause vm
- `virsh resume vm`: running vm pause
- `virsh console vm`: console vào vm
- `virsh shutdown vm`: tắt vm
- `virsh destroy vm`: buộc shutdown vm
- `virsh undefine vm`: xóa vm
- `virsh edit vm`: sửa file cấu hình vm
- `virsh dumpxml vm > vm.xml`: export file cấu hình xml của vm
- `virsh define vm.xml` : tạo vm từ file xml
- `virsh list`: list vm running
- `virsh list --all`: list all vm without running
- `# systemctl enable serial-getty@ttyS0.service` enable console 
- `# systemctl start serial-getty@ttyS0.service start` console

**3. CPU & CPU Pinning**
CPU PINNING: Giúp lien ket vCPU cua VM vao Physical CPU mong muon. Giup tan dung toi da phan cung cua CPU tang hieu nang cho cac task hoac procces cu the cho VM

- `virsh vcpupin centos7 1 2` :  Pin vcpu thread ID 01 vào physical CPU 2 
- `virsh vcpupin centos7` Show vcpu pin

**4. Limit CPU**
- List info vcpu `# virsh vcpucount centos7`
- Modify maximum config `# virsh setvcpus <VM> <max-number-of-CPUs> --maximum --config`
- Modify current config: `# virsh setvcpus <VM> <number-of-CPUs> --config`

**5. Interface action**
- List NIC
```
root@virsh01:~# virsh domiflist centos7
Interface  Type       Source     Model       MAC
-------------------------------------------------------
vnet0      bridge     virbr0     virtio      52:54:00:27:8a:34
vnet1      bridge     nambr      virtio      52:54:00:4b:73:5f
```
- Add NIC to VM
```
# virsh attach-interface --domain centos7 --type bridge --source nambr --model virtio --mac 52:54:00:4b:73:5f --config --live
```
- Remove NIC
```
# virsh detach-interface --domain centos7 --type bridge --mac 52:54:00:4b:73:5f --config --live
```
**7. Limit bandwidth** 
- Thêm thẻ bandwidth vào thẻ interface file cấu hình VM 
```
<interface type='bridge'>
  <mac address='52:54:00:27:8a:34'/>
  <source bridge='virbr0'/>
  <bandwidth>
    <inbound average='625' peak='625' burst='625'/>
    <outbound average='625' peak='625' burst='625'/>
  </bandwidth>
  <model type='virtio'/>
  <address type='pci' domain='0x0000' bus='0x00' slot='0x03' function='0x0'/>
</interface>
```
- Shutdown VM và Start lại check
```
[root@localhost ~]# speedtest
Retrieving speedtest.net configuration...
Testing from Viettel Group (27.72.98.253)...
Retrieving speedtest.net server list...
Selecting best server based on ping...
Hosted by Viettel Network (Hanoi) [0.41 km]: 7.919 ms
Testing download speed................................................................................
Download: 4.69 Mbit/s
Testing upload speed................................................................................................
Upload: 5.35 Mbit/s
```
**8. Manage VM ram**
```
$ sudo virsh setmaxmem centos7 2048 --config
$ sudo virsh setmem centos7 2048 --config
```
**9.  Volume action**
- List disk
```
# virsh domblklist centos7
```
- Create new disk format qcow2 (Có các loại format khác phổ biến nhất trong kvm là raw và qcow2
```
# cd /var/lib/libvirt/images/
# qemu-img create -f qcow2 centos7-disk2-2g 2G
or
# virsh vol-create-as default centos7-disk2-2g 2G 
```
- Attach disk to VM
```
root@virsh01:~# virsh attach-disk centos7 --source /var/lib/libvirt/images/centos7-disk2-2g --target vdb --persistent
```
- Remove disk from VM
```
virsh detach-disk --domain centos7 /var/lib/libvirt/images/centos7-disk2-2g --persistent --config --live
```
- Check disk info
```
# qemu-img info centos7.qcow2
```
- Convert format disk qcow2 to raw
```
# qemu-img convert -f qcow2 -O raw centos7.qcow2 centos7.img
```
- Resize disk
```
# qemu-img resize -f raw /var/lib/libvirt/images/centos7.img 5G
```
**10. Migrate action**
- Migrate offline:
```
root@virsh02:~# virsh -d 0 migrate --offline --persistent centos7_01 qemu+ssh://virsh01/system
setlocale: No such file or directory
migrate: offline(bool): (none)
migrate: persistent(bool): (none)
migrate: domain(optdata): centos7_01
migrate: desturi(optdata): qemu+ssh://virsh01/system
migrate: found option <domain>: centos7_01
migrate: <domain> trying as domain NAME
migrate: found option <domain>: centos7_01
migrate: <domain> trying as domain NAME
```
- Migrate Live
```
root@virsh02:~# virsh migrate --live --verbose centos7_01 qemu+ssh://virsh01/system
Migration: [100 %]
```
- Option:
  `--verbose:` hien thi qua trinh migrate
  `--live:` migrate khi vm dang running
  `--unsafe:` bo qua ca thu tuc an toan
  `--tunnelled:` migarte sy dung tunnel
  `--suspend:` dat trang thai vm pause sau khi migrate
  `--direct:` su dung de dieu huong truc tiep
  `--dname:` doi ten vm sau khi migrate
  `--persistenta:` giu vm o trang thai persistent tren host đích, cần su dung khi migrate offline.
  
  Link tham khao: https://computingforgeeks.com/virsh-commands-cheatsheet/
