---
layout: post
title: "libvirt-php 学习小记"
subtitle: ""
category: PHP
author: "寞踪"
header-img: "img/post-bg-2015.jpg"
tags: [PHP,Libvirt]
---

Libvirt 库是一种实现 Linux 虚拟化功能的 Linux® API，它支持各种虚拟机监控程序，包括 Xen 和 KVM，以及 QEMU 和用于其他操作系统的一些虚拟产品。

一、安装libvirt环境

(1)、yum install virt-manager libvirt libvirt-python python-virtinst -y

(2)、升级

    yum -y install gcc gcc-c++
    yum install libxml2-devel gnutls-devel device-mapper-devel python-devel libnl-devel -y
    wget http://libvirt.org/sources/libvirt-1.1.1.tar.gz
    tar xzvf libvirt-0.9.4.tar.gz
    cd libvirt-0.9.4
    ./configure
    make && make install

    [root@localhost ~]# service libvirtd start

virsh -version 可能会报错

1、error: Failed to connect socket to '/usr/local/var/run/libvirt/libvirt-sock': No such file or directory

做个链接：

    ln -s /var/run/libvirt/libvirt-sock /usr/local/var/run/libvirt/libvirt-sock

2、libvirt: Remote error : unable to connect to '/usr/local/var/run/libvirt/libvirt-sock-ro': No such file or directory

再做个链接

    ln -s /var/run/libvirt/libvirt-sock-ro /usr/local/var/run/libvirt/libvirt-sock-ro

    [root@localhost ~]# virsh -version
    0.10.0
    [root@localhost ~]# libvirtd --version
    libvirtd (libvirt) 1.1.1

重启下电脑

    [root@localhost ~]# virsh -version
    1.1.1

二、创建虚拟机

1、demo.xml

    <domain type='kvm'>
        <name>myvm</name> <!-- 名称 -->
        <memory>10485760</memory> <!-- 内存 -->
        <currentMemory>10485760</currentMemory>
        <vcpu>1</vcpu>
        <os>
            <type arch='x86_64' machine='pc'>hvm</type> <!-- 系统 -->
            <boot dev='hd'/> <!-- 从硬盘启动 -->
        </os>
        <features>
        <acpi/>
        <apic/>
        <pae/>
        </features>
        <clock offset='localtime'/>
        <on_poweroff>destroy</on_poweroff>
        <on_reboot>restart</on_reboot>
        <on_crash>destroy</on_crash>
        <devices>
            <emulator>/usr/libexec/qemu-kvm</emulator>
            <disk type='file' device='disk'>
                <source file='/data/instances/myvm/disk.qcow2'/>
                <target dev='sdb' bus='virtio'/>  <!-- 用sdb -->
            </disk>
            <disk type='file' device='disk'>
                <driver name='qemu' type='qcow2'/>
                <source file='/data/instances/myvm/swap.qcow2'/>
                <target dev='sdb' bus='virtio'/>
            </disk>
            <disk type='file' device='disk'>
                <driver name='qemu' type='qcow2'/>
                <source file='/data/instances/myvm/disk2.qcow2'/>
                <target dev='sdc' bus='virtio'/>
            </disk>
            <disk type='file' device='cdrom'>
                <driver name='qemu' type='raw'/>
                <source file='/data/instances/myvm/meta.iso'/> <!-- 这里是有作用的，稍后解释 -->
                <target dev='hda' bus='ide'/>
                <readonly/>
            </disk>
            <interface type='bridge'>
                <source bridge='virbr0'/>
                <mac address="00:16:3e:5d:aa:a8"/>
                <filterref>
                    <parameter name='IP' value='192.168.1.1' />
                </filterref>
            </interface>
            <input type='mouse' bus='ps2'/>
            <graphics type='vnc' port='-1' autoport='yes' keymap='en-us' listen='0.0.0.0'/> 
            <!-- ip 端口5900 累计相加 -->
        </devices>
    </domain>

2、定义KVM

    [root@localhost ~]# virsh define demo.xml 
    Domain myvm defined from demo.xml

3、启动KVM

    [root@localhost ~]# virsh start myvm
    Domain myvm started

myvm 只的是demo.xml里的虚拟机名称

4、查看vnc信息

    [root@localhost ~]# virsh vncdisplay myvm
    127.0.0.1:2

5、如何修改

    virsh edit <Name of KVM>

6、查看运行中的虚拟机

    [root@localhost ~]# virsh list
    [root@localhost ~]# virsh list --all

7、meta.iso 是什么呢？

创建完一台虚拟机后，要给虚拟机分配IP，挂载磁盘，所以要先把要配置的信息存入一个文件，做成iso，然后挂载上，虚拟机在开机的时候，挂载上文件，读取他，处理他。这个脚本，运维的人会写，很简单的事。格式自己定。

例如meta.js文件转成meta.iso的命令是：mkisofs -r -o meta.iso meta.js

三、用python创建虚拟机

demo.xml  注意，虚拟机名字要换一下

    #encoding:utf8
    import libvirt

    uri='qemu:///system'
    conn =libvirt.open(uri) #这里要用读写的方式打开连接  

    with open('demo.xml') as f:  
        xml = f.read()  

    domain = conn.defineXML(xml)  
    domain.createWithFlags(0)  
      
    try:  
        dom0 = conn.lookupByName("pyvm")  #虚拟机名称
    except:  
        print 'Failed to find the main domain'  
        sys.exit(1)  
      
    print "Domain 0: id %d running %s" % (dom0.ID(), dom0.OSType())  
    print dom0.info()


四、用php创建虚拟机

再安装依赖包：yum -y install gcc gcc.c++ zlib libxml2 libxml2-devel libmcrypt libcrypt-devel libmhash libjpeg libpng

然后安装PHP APACHE MYSQL

    yum -y install httpd httpd-devel mysql-server mysql-devel php php-devel php-gd php-mysql

然后再装libvirt-php

    wget http://libvirt.org/sources/php/libvirt-php-0.4.8.tar.gz
    tar -zxvf libvirt-php-0.4.8.tar.gz

然后

    cd libvirt-php*
    ./configure

编译的时候如果报错，提示You need libvirt 说明libvirt开发包没有安装好，运行

    yum -y install libvirt-devel

最后运行

    make && make install

建一个测试php文件

    print_r( libvirt_version() );  // 有结果，就表示，安装成功啦


可能有人不喜欢apache，ok，就安装nginx呗，顺便 yum -y install php-fpm

启动service php-fpm start

这里就不详细说啦

从 https://github.com/xpmozong/libvirt-php-examples 下载例子

[root@localhost examples]# php create.php  创建
[root@localhost examples]# php test.php    列出虚拟机列表

其中libvirt.php 里有很多函数，没写全，要添加的话参考http://libvirt.org/php/api-reference.html

php有执行libvirt的权限，但是apache、nginx没有这个权限，所以要赋予权限。

还有通过web访问，要设置libvirt的认证，vim /etc/libvirt/libvirtd.conf 文件 auth_tcp = "none"

brctl show 查看网桥

跟重要的是，要有VNC啊，推荐 [noVNC](http://kanaka.github.io/noVNC/)