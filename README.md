# Tạo image 
------------

## 1. Các cách tạo image

 a. Tải và dùng trực tiếp image đã có sẵn từ trên mạng
 
 b. Tạo image bằng dòng lệnh
 
 c. Tạo image bằng giao diện Virtual Machine trên Ubuntu1204 desktop

## 2. Các bước chuẩn bị

 a. Giao diện
 
Cài đặt Ubuntu desktop 12.04
 
 b. Chuẩn bị Hypervisor
 
Trên Ubuntu12.04 desktop cài đặt Qemu-kvm

    apt-get install -y kvm qemu-kvm libvirt-bin virtinst bridge-utils

Enable vhost-net

	modprobe vhost_net
	lsmod | grep vhost
	echo vhost_net >> /etc/modules
 
Cài đặt Virt-Manager
 
    apt-get install virt-manager hal -y
 
## 3. Thực hiện

#### a. Tải và dùng trực tiếp image đã có sẵn từ trên mạng

Tải image ubuntu 13.10 trên máy chủ cài OpenStack
 
    wget http://cloud-images.ubuntu.com/releases/13.10/release/ubuntu-13.10-server-cloudimg-amd64-disk1.img -P /var/kvm/images

    glance image-create --name="Ubuntu1310" --is-public=true --disk-format=qcow2 --container-format=bare < /var/kvm/images/ubuntu-13.10-server-cloudimg-amd64-disk1.img	

#### b. Tạo image bằng dòng lệnh
Tạo file image của máy ảo theo định dạng qcow2 trong thu mục <i>/var/kvm/images</i>, sử dụng qemu
 
    qemu-img create -f qcow2 ubuntu1204.img 10G
Tạo máy ảo từ file iso trên file image vừa tạo

    virt-install -n ubuntu1204 -r 1024 --vcpus 2 --os-type=linux --os-variant=ubuntupercise --disk path=/var/kvm/image/ubuntu1204.img,format=qcow2,bus=virtio,cache=none -w network=default,model=virtio --vnc --noautoconsole --c /home/uycn/ubuntu-12.04.3-server-amd64.iso	
Nén file .img và định dạng qcow2 cho image

    qemu-img convert -c ubuntu1204.img -O qcow2 ubuntu1204.qcow2

Đẩy image lên máy chủ OpenStack

    scp ubuntu1204.qcow2 root@192.168.1.145:/root

Upload image lên image service (chạy lệnh này trên máy chủ OpenStack)

    glance image-create --name "ubuntu1204" --disk-format qcow2 --container-format bare --is-public True --progress < ubuntu1204.qcow2

Kiểm tra xem image tạo có thành công hay không (có thể vào dashboard để kiểm tra)

    glance image-list
	
#### c. Tạo image bằng giao diện sử dụng virtual machine manager trên ubuntu12.04 desktop

Vào terminal gõ <i>virt-manager</i>  cửa sổ virt-manager hiện lên

![img](http://i.imgur.com/cgB2hcp.png "img")

Bắt đầu tạo máy ảo  

![img](http://i.imgur.com/yv4RDIK.png "img")
Điền tên của máy ảo và chọn install bằng file iso
 
![img](http://i.imgur.com/yv4RDIK.png "img") 
 
 
 
 
 
Nén file .img và định dạng qcow2 cho image

    qemu-img convert -c ubuntu1204.img -O qcow2 ubuntu1204.qcow2

Đẩy image lên máy chủ OpenStack

    scp ubuntu1204.qcow2 root@192.168.1.145:/root

Upload image (chạy lệnh này trên máy chủ OpenStack)

    glance image-create --name "ubuntu1204" --disk-format qcow2 --container-format bare --is-public True --progress < ubuntu1204.qcow2

Kiểm tra image

#### d. Tạo image Win2k8r2
Tạo file image của máy ảo  theo định  dạng qcow2, trong thư mục <i>/var/image/win2k8r2</i>

    qemu-img create -f qcow2 /var/image/win2k8.img 10G

Tải driver cho image win2k8r2 lưu trong thư mục <i>/var/lib/libvirt/images/</i>

    wget https://launchpad.net/kvm-guest-drivers-windows/20120712/20120712/+download/virtio-win-drivers-20120712-1.iso
	
Tạo image win2k8r2 bằng lệnh

    virt-install -n Win2k8r2 -r 2048 --vcpus 2 --os-type=windows --os-variant=win2k8 --disk path=/var/image/win2k8r2.img,format=qcow2,bus=virtio,cache=none --disk path=/var/lib/libvirt/images/virtio-win-drivers-20120712-1.iso,device=cdrom -w network=default,model=virtio --vnc --noautoconsole -c /home/uycn/Win2k8R2.ISO

Vào Virtual Manager và bắt đầu cài đặt 

- Click Install
- chọn your Operating System type
- Accept License Terms
- chọn Custom Installation
- Click 'Load Driver'
- Click Browse
- click 'Virtio Drivers' thấy 2 cdroms attached. install cdrom and the other the 'Virtio Drivers'
- chọn 'Virtio Drivers' => STORAGE => SERVER2008R2 => AMD64
- Click OK
- The 'Red Hat VirtIO SCSI controller' driver should be highlighted. If not then you have done something wrong.
- Click Next Driver should load without error and take you back to the screen to select the hard drive to install the Operating System on
- Click 'Drive options'
- Click 'New' Ensure the entire drive space is being used - in our case the entire 10G
- Click 'Apply'
- Click 'OK'
- Brow lại và cài đặt thêm NETWORK driver
- Click 'Next' The Operating System will install. This process may take awhile depending on resources you gave initially. Be patient.
- Once completed you should be prompted to changed the Administrator password. Do so now.
- The install should be completed and log you in! Congrats.

###### Cài đặt date & time

- From the 'Initial Configuration Tasks' screen
- Click 'Set time zone'
- From the 'Date and Time' window Click 'Change time zone...'
- From the 'Time zone:' drop-down select your time zone.
- Click 'OK'
- Click 'OK'

###### Bật remote desktop

- From the 'Initial Configuration Tasks' screen
- Scroll down to '3. Customize This Server'
- Click 'Enable Remote Desktop'
- At the 'System Properties' window the 'Remote' tab under 'Remote Desktop' select your preferred level For my purposes I selected the middle option - the 'less secure' option
- Click 'OK'

###### Cấu hình firewall

- From the 'Initial Configuration Tasks' screen
- Scroll down to '3. Customize This Server'
- Click 'Configure Windows Firewall'
- At the 'Windows Firewall' click 'Turn Windows Firewall on or off'
- At the 'Customize setting for each type of network'
- Click 'Turn off Windows Firewall (not recommend)' for both Private and Public locations.
- Click 'OK'

###### Add thêm drivers

- The 'Sever Manager' Console should be showing up after you closed out the 'Initial Configuration Task' Window. You should now take the time to install the VirtIO Balloon Driver.
- Expand 'Diagnoistics' from the left hand window pane
- Select 'Device Manager'
- From the top menu click 'Action'
- Select 'Add Legacy Hardware'
- At the 'Add Hardware' window click 'Next'
- Click 'Install the hardware that I manually select from a list (Advanced)'
- Click 'Next'
- Leave 'Show All Devices' select - Click 'Next'
- Click 'Have Disk...'
- Click 'Browse'
- From the VirtIO CD - Locate Balloon => Server2008R2 => AMD64
- Click 'Open'
- Click 'OK'
- 'VirtIO Balloon Drive' should be highlighted - Click 'Next'
- Click 'Next'
- When prompted - select 'Install this driver software anyway'
- Click 'Finished'
	
	 