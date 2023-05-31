[toc]

##Contents

- [grep](#grep)
- [sed](#sed)
- [vim](#vim)
- [时区](#时区)
- [ln](#ln)
- [nc](#nc)
- [for](#for)
- [while](#while)
- [fsck](#fsck)
- [git](#git)
- [g++](#g++)
- [samba](#samba)
- [mac](#mac)
- [os](#os)
- [disk](#disk)
- [disk速度测试](#disk速度测试)
- [ps](#ps)
- [unicode_to_utf-8](#unicode_to_utf-8)
- [ubunt_disk_4TB](#ubunt_disk_4TB)
- [network](#network)
- [docker](#docker)
- [python3](#python3)
- [kernel](#kernel)
- [iis](#iis)

#grep

##当前目录包含字符串文件

    grep -rn "#include <assert" *
    docker ps -as | grep -E "cc-|ccc"

##删除100天前文件

    find ./xml_cdr -mtime +100 -type f -name *.xml -exec rm -rf {} \;

    //mv所有media下2天前文件
    find /app/media -type f -mtime +2 |xargs -i sudo mv -f {} /app/media.bak
    
    sudo mv -f /app/msg/`date -d "2 days ago" +%Y%m%d`.txt /app//msg.bak/

##不可见字符

    grep --color='auto' -P -n "[^\x00-\x7F]" file.xml
    sed 's/\x0b/ /g' b.txt > c.txt


#sed

    sed -n 's/0.0.0.0/1.1.1.1/p' a.txt
    sed -i 's/107.25.6.7/172.20.20.1/g' *.xml
    sed -i 's/1\.1\.1\.1/0\.0\.0\.0/g' a.txt
    sed -i "s/qh0/$qh0/g" `grep -rl 'qh0' --include="*.sh" --include="*.conf" --include="*.yml" --exclude="*.bash" ./`

    替换\r\n->\n
    sed -i "s/\r//" file_name
    find /home/test -name "*.sh" | xargs dos2unix
    dos2unix file_name


#vim

    整页翻页 ctrl-f ctrl-b
    } 移动到下一个block
    { 移动到前一个block
    w 移动到下一个word
    b 移动到上一个word
    0: 移动到行首
    $: 移动到行尾
    
    vim -b xx.so 二进制打开
    :set display=uhex 或者 :set dy=uhex
    :%!xxd -g 1  十六进制

    :%s/aa/bb/g  替换
    :%s/\r//g    ##\r\n->\n
    TAB 键替换为空格
    :set ts=4
    :set expandtab
    :%retab!


#时区

    sudo tzselect
    ...
    sudo cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
    date
    sudo ntpdate time.windows.com

    //自动同步时间
    sudo ntpdate time.nist.gov
    sudo ntpdate -s time.windows.com
    sudo apt-get install ntpdate
    sudo nepdate cn.pool.ntp.org

    sudo vim /etc/default/locale
    LC_TIME="en_DK.UTF-8"

#ln

    ln -s s->t
    ln -s ../bin/python3.8 /usr/local/bin/python3
    mklink /d C:\.nuget E:\.nuget

#nc

    nc -l 127.0.0.1 5300
    while true; do nc -l 5300; done

#for

    length2=`echo $json2 | jq '.hits.hits|length'`;
    if [ $length22 -eq 0 ];then
        msgtime22=-1
    fi

    for j in `seq 0 $length2`
    do
        s2=`echo $list2 | jq -c ".[$j]"`;
        if [ $s2 = "null" ];then
            continue
        fi

        if [ $(($i % 1000)) -eq 0 ];then  
            echo `date` $i/$length "updatetime:" $json4 $i $j $_id $_id2 $msgid2 $msgtime2 > /dev/console
        fi

        if [ "$msgid2" =  "\"\"" ];then
            continue
        fi

    done


    a=`redis-cli -h 127.0.0.1 -p 6379 get tradedate:SH`
    for i in 1 2 3 4 5 6 7 8 9 10 11 12 13 14
    do
        b=`date -d "$a -$i day" +%Y%m%d`
        echo $b >> /tmp/redis-del.log 2>&1;
        redis-cli -h 127.0.0.1 -p 6379 KEYS "*:$b" |xargs redis-cli -h 127.0.0.1 -p 6379 DEL >> /tmp/log.txt 2>&1;
    done
    echo `date` "end del ....." >> /tmp/log.txt

#while

    #!/bin/bash
    while getopts "p:u:a:r:d:o:" opt;do
    case $opt in
    p)
        gw_fix_phone=$OPTARG
        ;;
    u)
        gw_username=$OPTARG
        ;;
    a)
        gw_auth_username=$OPTARG
        ;;
    r)
        gw_realm=$OPTARG
        ;;
    d)
        gw_from_domain=$OPTARG
        ;;
    o)
        gw_outbound_proxy=$OPTARG
        ;;
    ?)
        echo "unkonw argument"
        exit 1
    ;;
    esac
    done
    mkdir $(pwd)/freeswitch_config/sip_profiles/external2
    rm -rf $(pwd)/freeswitch_config/sip_profiles/external2/gw${gw_fix_phone}.xml
    sed -e "s/%gw_fix_phone%/${gw_fix_phone}/;s/%gw_username%/${gw_username}/;s/%gw_auth_username%/${gw_auth_username}/;s/%gw_realm%/${gw_realm}/;s/%gw_from_domain%/${gw_from_domain}/;s/%gw_outbound_proxy%/${gw_outbound_proxy}/" $(pwd)/template/external2_00000000.xml > $(pwd)/freeswitch_config/sip_profiles/external2/gw${gw_fix_phone}.xml;

#fsck

    fsck /dev/mapper/ubuntu--vg-ubuntu--lv
    fsck /dev/sda1
    fsck -fvy /
    chkdsk c: /f
    sudo mount -o remount,rw /partition/identifier /mount/point
    sudo mount -o remount, rw /dev/mapper/ubuntu--vg-ubuntu--lv /
    mount -v | grep "^/" | awk '{print "\nPartition identifier: " $1  "\n Mountpoint: "  $3}'

#git

    git show 显示最后一次的文件改变的具体内容
    git show -5 显示最后 5 次的文件改变的具体内容

    撤消本地修改
    git fetch --all
    git reset --hard origin/master

    git log --name-status 每次修改的文件列表, 显示状态
    git log --name-only 每次修改的文件列表
    git log --stat 每次修改的文件列表, 及文件修改的统计
    git whatchanged 每次修改的文件列表
    git whatchanged --stat 每次修改的文件列表, 统计
    git show commitid 显示某个 commitid 改变的具体内容

    回退到某个版本
    git checkout 7cd0386bd67e7f240b55fd037159ff7fe8f7063b seg_sensitive.txt

#g++

    sudo vim /etc/profile
    export LD_LIBRARY_PATH=/usr/local/lib:${LD_LIBRARY_PATH}
    source /etc/profile

#samba

    sudo apt-get install samba samba-common
    sudo vim /etc/samba/smb.conf

    [global]
    security = user

    [mywork]
    comment = test
    path = /home/test/mywork
    available = yes
    create mask = 64
    writeable = yes
    browseable = yes
    valid users = test

    //已存在test用户,设置密码
    sudo smbpasswd -a test
    sudo service smbd restart

#mac
    
    locate tf-keras-datasets
    sudo /usr/libexec/locate.updatedb

#os
    禁止休眠:
    sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target

##windows命令窗口中文乱码
    
    chcp 65001
    chcp 936

#disk

    1.对新增加的硬盘进行分区、格式化

    增加空间的硬盘是 /dev/sda 
    分区： 
    [root@localhost]# fdisk /dev/sda     
    p       查看已分区数量(我看到有两个 /dev/sda1 /dev/sda2)
    n       新增加一个分区 
    p       分区类型我们选择为主分区 
            分区号选3(因为1,2已经用过了，见上)
    回车      默认(起始扇区)
    回车      默认(结束扇区) 
    t       修改分区类型 
            选分区3 
    8e       修改为LVM（8e就是LVM） 
    w        写分区表 
    q        完成，退出fdisk命令

    使用partprobe 命令 或者重启机器 
    格式化分区
    mkfs.ext4 /dev/sda3

    2添加新LVM到已有的LVM组，实现扩容

    lvm              进入lvm管理
    lvm>pvcreate /dev/sda3      这是初始化刚才的分区，必须的(将物理分区创建为物理卷)(in linux lvm(ext4))
    lvm>vgextend ubuntu-vg /dev/sda3  将初始化过的分区加入到虚拟卷组ubuntu-vg (卷和卷组的命令可以通过  vgdisplay, vgextend ubuntu-vg /dev/sda3)
    lvm>vgdisplay -v
    lvm>lvextend -l+953861 /dev/mapper/ubuntu--vg-ubuntu--lv //扩展已有卷的容量（953861 是通过vgdisplay查看的Free  PE / Size的大小） 
    lvm>pvdisplay                            查看卷容量，这时你会看到一个很大的卷了
    lvm>quit                                  退出

    3以上只是卷扩容了，下面是文件系统的真正扩容，输入以下命令：

    sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv (系统安装后，扩容默认大小)

    4查看新的磁盘空间
    df -h
    ------------------------------------------------------------------------------
    //lvm 调整大小
    sudo vgdisplay
    lvextend -L 120G /dev/mapper/ubuntu--vg-ubuntu--lv     //增大至120G
    resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv  //执行调整
    lvextend -L 120G /dev/mapper/ubuntu--vg-ubuntu--lv     //增大至120G
    df -h
    ------------------------------------------------------------------------------

    sudo fdisk -lu
    sudo fdisk /dev/sdb
    m
    n
    p
    1
    ->
    ->
    p
    w

    sudo fdisk -lu
    sudo mkfs.ext4 /dev/sdb
    sudo df -l
    sudo mount -t ext4 /dev/sdb /SDB1
    sudo df -l
    sudo vim /etc/fstab
    LABEL=cloudimg-rootfs   /  ext4   defaults        0 0
    /dev/sdb                /SDB1    ext4   defaults        0 0
    -----------------------------------------------------------------------------

#disk速度测试

    sudo time dd if=/dev/zero of=test.dat bs=8k count=130000
    sudo time dd if=/dev/sda of=/dev/null bs=8k count=130000
    sudo time dd if=/dev/sda of=testrw.dat bs=8k  count=130000

#ps

    ps -axj
    ps -e
    //handle count
    ls /proc/25091/fd | wc -l
    ls -asl /proc/<pid>/fd/
    lsof -p 25091
    //thread
    top -H -p 25091
    pstree -p 进程号
    //memory
    ps -aux

#unicode_to_utf-8
    
    file -i s.txt
    iconv -f utf-16 -t utf-8 s.txt > s2.txt
    iconv -f GBK -t UTF-8 seg.txt -o seg.txt.utf8

#ubunt_disk_4TB

    sudo parted /dev/sda #进入parted 
    mklabel gpt          #将磁盘设置为gpt格式，
    mkpart logical 0 -1  #将磁盘所有的容量设置为GPT格式
    print                #查看分区结果
    这个时候应该是默认进行分了一个/dev/sda1这个分区
    退出parted
    quit
    终端输入 sudo mkfs.ext4 -F /dev/sda1 
    将刚刚分出来的sda1格式化为ext4的格式，然后就可以设置开机自动挂载了。
    sudo mount -t ext4 /dev/sda1 /app

    设置开机自动挂载
    查看硬盘/dev/sda1 对应的UUID
    sudo blkid   
    注意: 唯一的sda1的UUID号。
    再事先准备好一个地方来做挂载点，比如我这里是/DATA4T然后再用命令打开配置文件：
    sudo vim /etc/fstab
    添加
    UUID=7941f2c5-d582-4414-85c5-6d199a701795 /app ext4    defaults 0       0
    重启电

#network

    cis@ubuntu:~$ cat /etc/netplan/01-netcfg.yaml 
    # This file describes the network interfaces available on your system
    # For more information, see netplan(5).
    network:
    version: 2
    renderer: networkd
    ethernets:
        ens33:
        dhcp4: no
        dhcp6: no
        addresses: [10.10.8.150/21]
        gateway4: 10.10.8.1
        nameservers:
            addresses: [114.114.114.114,10.10.8.1]

#docker

    curl -k -sSl https://get.docker.com | sudo sh
    curl -fsSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    sudo usermod -aG ${USER} 
    or >sudo usermod -aG docker $USER

    sudo docker ps -a | grep "Exited" | awk '{print $1}'| xargs docker stop
    sudo docker ps -a | grep "Exited" | awk '{print $1}'| xargs docker rm
    sudo docker images | grep none | awk '{print $3}'| xargs docker rmi

    #!/bin/bash
    sum=`docker images| wc -l`
    echo images:$sum
    COUNT=`expr $sum - 1`
    echo count:$COUNT
    TAG=`docker images |grep -v REPOSITORY|awk '{print $1":" $2}'|awk 'ORS=NR%"'$COUNT'"?" ":"\n"{print}'`
    echo TAG:$TAG
    docker save $TAG > ./test.tar
    echo endend
    ######################
    #!/bin/bash
    docker load -i test.tar


#python3

    apt-get install python3
    apt-get install python3-pip
    //////////////////////////////
    or windows
    python -m pip install --upgrade pip --force-reinstall
    //////////////////////////////
    pip3 install --upgrade pip

#kernel

    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-headers-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-headers-6.3.0-060300_6.3.0-060300.202304232030_all.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-image-unsigned-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-modules-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    sudo dpkg --install *.deb
    sudo reboot
    uname -r

#iis

    # 导出所有应用程序池
    C:\Windows\System32\inetsrv\appcmd.exe list apppool /config /xml > d:\temp\apppools.xml

    # 导入所有应用程序池
    C:\Windows\System32\inetsrv\appcmd.exe add apppool /in < d:\temp\apppools.xml

    # 导出所有站点
    C:\Windows\System32\inetsrv\appcmd.exe list site /config /xml > d:\temp\sites.xml

    # 导入所有站点
    C:\Windows\System32\inetsrv\appcmd.exe add site /in < d:\temp\sites.xml


    # 导出单独的应用程序池
    C:\Windows\System32\inetsrv\appcmd.exe list apppool "应用程序池名称" /config /xml > c:myapppool.xml
    # 导入单独的应用程序池
    C:\Windows\System32\inetsrv\appcmd.exe add apppool /in < c:myapppool.xml
    # 导出单独站点
    C:\Windows\System32\inetsrv\appcmd.exe list site "站点名称" /config /xml > c:mywebsite.xml
    # 导入单独站点
    C:\Windows\System32\inetsrv\appcmd.exe add site /in < c:mywebsite.xml

