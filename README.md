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
Tạo file image của máy ảo theo định dạng qcow2 thong thu mục /var/kvm/images, sử dụng qemu
 
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

Vào terminal gõ <i>virt-manager</i> 

Bắt đầu tạo máy ảo  
 
 
 
 
 
 
 
 
Nén file .img và định dạng qcow2 cho image

    qemu-img convert -c ubuntu1204.img -O qcow2 ubuntu1204.qcow2

Đẩy image lên máy chủ OpenStack

    scp ubuntu1204.qcow2 root@192.168.1.145:/root

Upload image lên image service (chạy lệnh này trên máy chủ OpenStack)

    glance image-create --name "ubuntu1204" --disk-format qcow2 --container-format bare --is-public True --progress < ubuntu1204.qcow2

Kiểm tra image


 
 