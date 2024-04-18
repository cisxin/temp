[toc]

##Contents

- [grep](#grep)
- [awk](#awk)
- [sed](#sed)
- [vim](#vim)
- [时区](#时区)
- [ln](#ln)
- [rsync](#rsync)
- [ssh](#ssh)
- [crontab](#crontab)
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
- [curl](#curl)
- [docker](#docker)
- [su_id](#su_id)
- [python3](#python3)
- [kernel](#kernel)
- [rhel9](#rhel9)
- [llm](#llm)
- [iis](#iis)
- [java　.keystore](#java　.keystore)

#grep

##当前目录包含字符串文件

    grep -rn "#include <assert" *
    docker ps -as | grep -E "cc-|ccc"

    findstr "hello there" ".\*.*"
    findstr /s /i /c:"hello there" /f:aa.txt

    echo 'Hello $USER'
    echo "Hello $USER"
    `echo "Hello $USER"`
    "`echo "Hello $USER"`"
    `echo "echo $USER"`
    echo "`echo "echo $USER"`"
    (echo 'Hello $USER';echo "Hello $USER")
    {echo 'Hello $USER';echo "Hello $USER"}

    $#：表示传递给脚本或函数的参数数量。
    $*：所有参数列表，作为一个单独的字符串。参数之间使用第一个字符的值作为分隔符（通常是空格）。
    $@：所有参数列表，每个参数都作为一个独立的字符串。每个参数都保留了它们各自的引号（如果有的话）。
    $?：上一个命令的退出状态码。0表示成功
    $()：命令替换语法，允许你将一个命令的输出嵌入到另一个命令中。例如：result=$(ls) 会将 ls 命令的输出存储在 result 变量中。=``
    ${}：用于在变量名周围进行扩展和操作的语法。例如：${variable} 将会扩展为变量的值。

    []:test（0表示真，1表示假）。在方括号内，条件表达式和运算符之间需要用空格分隔。例如：
    if [ "$var" = "value" ]; then
        echo "Condition is true"
    fi
    [[]]:支持更复杂的逻辑操作，字符串比较等
    if [[ "$var"== "value" ]]; then
        echo "Condition is true"
    fi

    echo "Please enter your name: "
    read name
    echo "Hello, $name!"
    read -n1 -p "Do you want to continue? (y/n): " choice
    echo
    if [ "$choice" == "y" ] || [ "$choice" == "Y" ]; then
        echo "You chose to continue."
    else
        echo "You chose to cancel."
    fi

    set | grep -E "^[A-Za-z_]+="

#awk

    awk -F',' '{ print $1, $3 }' filename
    //数值计算，求和...
    awk '{ total += $1 } END { print "Total:", total }' filename
    awk 'END { print NR, NF }' filename

    awk '$3 > 50 { print $1, $2 }' filename
    awk '{ printf "Name: %-10s Age: %d\n", $1, $2 }' filename
    //自定义变量
    awk -v threshold=50 '$3 > threshold { print $1, $2 }' filename

    awk '/pattern/ { print $1, $3 }' logfile.txt | perl -ne 'print if /regex/'
    perl -pe 's/pattern/replacement/g' file.txt | awk '{ printf "Processed: %s\n", $0 }'
     
    //sum
    seq 5 |awk 'BEGIN{sum=0;print "总和:"}{if(NR<=4)printf $1"+";sum+=$1; if(NR==5)printf $1 "="}END{print sum}'

    tar -tvf 20231031.tar | awk '{a+=$3}END{print a}'

    ps -ef | grep msgbak | grep -v  grep | awk '{print $2}' | xargs kill -9

    ls -l --time-style=+"%Y-%m-%dT%H:%M:%S" | grep 2023-08 | awk '{print $7}' | xargs ls -lah
    ls -l --time-style=+%Y%m%d | awk '{if (length($6) > 0 && $6 < 20230808) {print $7}}' | xargs -r ls 

##删除100天前文件

    find ./xml_cdr -mtime +100 -type f -name *.xml -exec rm -rf {} \;

    //mv所有media下2天前文件
    find /app/media -type f -mtime +2 |xargs -i mv -f {} /app/media.bak
    
    sudo mv -f /app/msg/`date -d "2 days ago" +%Y%m%d`.txt /app//msg.bak/

    -----------------------------------
    #!/bin/bash
    t1=$1
    t2=$2
    p0=$3
    n0=$4
    echo $t1 $t2 $p0 $n0
    cd $p0
    #find ./ -name "*"  -newermt $t1 ! -newermt $t2 | xargs -exec  tar -cvf /tmp/$n0
    find_result=$(find ./ -name "*"  -newermt $t1 ! -newermt $t2 | grep -wv "./")
    #echo "find_result:" $find_result
    if [ -n "$find_result" ]; then
        find ./ -name "*"  -newermt $t1 ! -newermt $t2 | grep -wv "./" | xargs -exec  tar -cvf /tmp/$n0
    else
        echo "no file"
    fi
    ------------------------

    find ./ -maxdepth 1 -type f -regextype posix-extended -regex '.*/[^/]{32,}$'
    find ./ -maxdepth 1 -type f -regextype posix-extended -regex '.*/[a-zA-Z0-9_/-/./]{8,}'
    find ./ -maxdepth 1 -type f -mtime +30 -regextype posix-extended -regex '.*/[a-zA-Z0-9_/-/./]{32,}' -exec rm -rf {} \;

##不可见字符

    grep --color='auto' -P -n "[^\x00-\x7F]" file.xml
    sed 's/\x0b/ /g' b.txt > c.txt


#sed

    sed -n 's/0.0.0.0/1.1.1.1/p' a.txt
    sed -i 's/107.25.6.7/172.20.20.1/g' *.xml
    sed -i 's/1\.1\.1\.1/0\.0\.0\.0/g' a.txt
    sed -i "s/qh0/$qh0/g" `grep -rl 'qh0' --include="*.sh" --include="*.conf" --include="*.yml" --exclude="*.bash" ./`

    替换\r\n->\n
    sed -i "s/\r//g" file_name
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

    head -n 1000 a.txt | tail -n +100
    tail -n +100 -n 900 a.txt
    
    :vim gbk utf-8
    vim ~/.vimrc
    let &termencoding=&encoding
    set fileencodings=utf-8,gb18030,gb2312,gbk,big5

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
    
    --------
    sudo timedatectl set-timezone Asia/Shanghai
    timedatectl


#ln

    ln -s s->t
    ln -s ../bin/python3.8 /usr/local/bin/python3
    mklink /d C:\.nuget E:\.nuget

#rsync

    rsync -av -e 'ssh -p 2234' source/ user@remote_host:/destination

    //--link-dest参数指定基准目录/compare/path，然后源目录/source/path跟基准目录进行比较，找出变动的文件，将它们拷贝到目标目录/target/path。那些没变动的文件则会生成硬链接。这个命令的第一次备份时是全量备份，后面就都是增量备份了。
    rsync -a --delete --link-dest /compare/path /source/path /target/path

    sudo apt-get install sshpass
    sshpass -p "123456" rsync -av --delete /tmp/aaaa test@192.168.0.1:/tmp

    sshpass -p "123456" rsync -av --progress --delete /tmp/aaaa test@192.168.0.1:/tmp >> /tmp/log.txt 2>&1
    echo `date` "rsync test end ....." >> /tmp/log.txt

#ssh

    ssh -i "test.pem" ubuntu@xxxx.cn-north-1.compute.amazonaws.com.cn
    scp -i
    scp -P port 01.py user@remote:Desktop/01.py
    scp -P port user@remote:Desktop/01.py 01.py
    scp -r demo user@remote:Desktop
    scp -r user@remote:Desktop demo
    shpass -p "123456" scp -P port 01.py user@remote:Desktop/01.py

    //在 ~/.ssh 目录下生成密钥对文件
    ssh-keygen -t rsa
    //ssh-keygen -m PEM -t rsa -b 4096
    # ==> id_rsa id_rsa.pub
    //复制id_rsa.pub到主机上指定用户的 ~/.ssh/authorized_keys
    cat id_rsa.pub >> authorized_keys
    //chmod 600 .ssh/authorized_keys #注意权限！//server
    //~/.ssh/known_hosts 文件存储了远程主机的公钥 //编辑文件更新已知主机的公钥信息 //client
    ssh-copy-id user@hostname //将公钥发布到服务器
    ssh-copy-id –i id_rsa.pub administrator@IP
    $rhel9
    ecdsa-sha2-nistp256
    ssh-keygen -t ecdsa
    ssh-copy-id -i id_ecdsa.pub trade@ip
    ssh-copy-id trade@ip


    //配置文件（~/.ssh/config）来区分和指定多个私钥文件对应的主机
    vim ~/.ssh/config
    Host host1
        HostName <hostname or IP>
        User <username>
        IdentityFile ~/.ssh/id_rsa1

    //putty
    @echo off & setlocal enabledelayedexpansion
    title wework
    set /p d1=startdate:
    echo %d1%
    set /a d1=%d1%
    set date_format="YYYYMMDD"
    echo !d1! | findstr /r /c:"^[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]"
    if %errorlevel% == 1 (
    echo 日期1格式不正确，应为 %date_format%
    exit /b 1
    )
    set /p d2=enddate:
    echo %d2%
    set date_format="YYYYMMDD"
    echo !d2! | findstr /r /c:"^[0-9][0-9][0-9][0-9][0-9][0-9][0-9][0-9]"
    if %errorlevel% == 1 (
    echo 日期2格式不正确，应为 %date_format%
    exit /b 1
    )
    plink.exe -ssh -no-antispoof -P 2244 -i E:\temp\id_rsa_putty.ppk ubuntu@x.x.x.x /tmp/run.sh !d1! !d2!
    echo download...
    pscp.exe -sftp -P 2244 -i E:\temp\id_rsa_putty.ppk -r ubuntu@x.x.x.x:/tmp/test ./
    echo end
    exit

#crontab
    
    etc/init.d/crond status
    sudo systemctl status cron.service

    59 23 * * * (cd /app; bash test.sh)
    10 01 * * 6 (sh /app/0.sh; sh /app/1.sh)


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

    git config --local credential.helper store 保存用户名密码

    回退到某个版本
    git checkout 7cd0386bd67e7f240b55fd037159ff7fe8f7063b seg_sensitive.txt

    git remote set-url origin https://ghp_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx@github.com/username/test.git
    
    中断 继续
    git clone --recursive https://huggingface.co/THUDM/chatglm3-6b
    
    ////////////////////
    第四步：Github账号上添加公钥
    进入Settings设置 -> SSH and GPG keys -> New SSH key - > 复制id_rsa.pub内容 -> 粘贴保存
    第五步：验证是否设置成功
    $ssh -T git@github.com
     successfully authenticated //表明设置成功
    不需要账号密码clone和push 注意:使用ssh的url

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

    ////rhel9
    rpm -qa | grep samba
    yum install samba
    rpm -qa | grep samba
    pdbedit -a -u trade
    pdbedit -L
    vim /etc/samba/smb.conf
    [bankfut]
            comment = bankfut
            path = /home/trade/asptools/bankfut
            writeable = yes
            browseable = yes
            read only = No
            create mask = 0664
            directory mask = 0775
            valid users = trade
    systemctl restart smb.service

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
    lvm>lvextend -l+953861 /dev/mapper/ubuntu--vg-ubuntu--lv //扩展已有卷的容量（953861 是通过vgdisplay查看的Free  PE / Size的大小） //redhat:lvextend -l+100%FREE /dev/mapper/rhel-opt 
    lvm>pvdisplay                            查看卷容量，这时你会看到一个很大的卷了
    lvm>quit                                  退出
    //lvdisplay /dev/mapper/rhel-opt

    3以上只是卷扩容了，下面是文件系统的真正扩容，输入以下命令：

    sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv (系统安装后，扩容默认大小)
    //redhat:xfs_growfs /dev/mapper/rhel-opt

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

    //rhel
    sudo lvs # 查看逻辑卷
    sudo vgs # 查看卷组
    sudo umount /dev/mapper/rhel-trade # 卸载逻辑卷
    sudo rm -rf dev/mapper/rhel-trade # 删除相关目录
    sudo lvremove dev/mapper/rhel-trade # 删除逻辑卷
    /////////////////////
    mdadm --zero-superblock /dev/sdh
    fdisk /dev/sdh //t fd
    mdadm -Cv /dev/md0 -n 6 -l 10 /dev/sd[h-m]
    mdadm -S /dev/md0
    mdadm -D /dev/md0
    mkfs.xfs /dev/md0
    //mkfs.xfs -f /dev/md0
    mount /dev/md0 /u02
    vim /etc/fstabl
    /dev/md0                /u02                    xfs     defaults        0 0
    //////////////////////

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

    kill 142 157
    kill -9 -- -117

    //强制关闭指定用户
    pkill -KILL -u username //who w
    
    //进程<=>端口号
    netstat -nap | grep 端口号

    //启动时间
    ps -eo pid,cmd,lstart | grep "example_process"

#unicode_to_utf-8
    
    file -i s.txt
    iconv -f utf-16 -t utf-8 s.txt > s2.txt
    iconv -f GBK -t UTF-8 seg.txt -o seg.txt.utf8
    cat ko.txt | iconv -f GBK -t UTF-8

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
    
    ip link

    cis@ubuntu:~$ cat /etc/netplan/01-netcfg.yaml 
    # This is the network config written by 'subiquity'
    network:
      ethernets:
        ens160:
          addresses:
          - 10.23.0.107/16
          gateway4: 10.23.0.1
          nameservers:
            addresses: []
            search:
            - 114.114.114.114
            - 114.114.114.114
        ens192:
          addresses:
          - 172.20.20.114/24
          nameservers:
            addresses: []
            search: []
      version: 2

    sudo vim  /etc/systemd/resolved.conf
    DNS=114.114.114.114 8.8.8.8
    sudo systemctl restart systemd-resolved

    //iperf:
    TCP服务端命令：iperf -s -i 1 -p 3389
    TCP客户端命令：iperf -c 172.19.16.97 -p 3389 -i 1

    UDP服务端命令:iperf -u -s -i 1 -p 3389
    UDP客户端命令:
    1、iperf -u -c 172.19.16.97 -p 3389 -b 1500M -i 1
    2、iperf -u -c 172.19.16.97 -p 3389 -b 2000M -i 1

    ip link show
    sudo ip link set eno4 up
    sudo ip link set eno4 down

#curl
    curl -H "Content-Type: application/json" -X POST -d '{"name":"test", "Company_name":"testtest", "mobile":"10086","status":1, "msg":"OK!" }' "http://10.1.1.5:8080/v1/api/insertdocument"

    ab -n 1000 -c 1000 "http://10.1.1.5:8080/v1/api/getdocument"
    ab -n400 -c20  -p "img.json" -T "application/x-www-form-urlencoded" "http://10.1.1.5:8080/v1/api/getdocument"

#docker

    curl -k -sSl https://get.docker.com | sudo sh
    curl -fsSL https://get.docker.com -o get-docker.sh
    sudo sh get-docker.sh
    sudo usermod -aG ${USER} 
    or >sudo usermod -aG docker $USER

    sudo docker ps -a | grep "Exited" | awk '{print $1}'| xargs docker stop
    sudo docker ps -a | grep "Exited" | awk '{print $1}'| xargs docker rm
    sudo docker images | grep none | awk '{print $3}'| xargs docker rmi

    //2
    curl -fsSL  https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add
    sudo add-apt-repository "deb [arch=amd64]  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" 
    sudo apt-get update
    sudo apt-get install docker-ce

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

    #!/bin/sh
    echo 123456 | sudo su << "EOF"
    echo "======== docker containers logs file size ========"  
    logs=$(find /var/lib/docker/containers/ -name *-json.log)  
    for log in $logs  
    do 
        echo `date` "clean logs : $log" >> /tmp/cleanlog.txt
        cat /dev/null > $log
        echo `date` "clean logs : $log"    
    done
    exit

    dockerfile:
    FROM ubuntu:latest
    RUN apt-get update -y
    RUN apt-get upgrade -y
    RUN apt-get install -y gcc g++ gdb make vim git wget  curl openssl libssl-dev
    RUN apt-get install -y python3 python3-pip
    RUN pip3 install --upgrade pip
    WORKDIR /app
    ADD ./ /app
    #COPY data /app/data
    #EXPOSE 80
    #VOLUME [ "/tmp":/tmp ]

    #cd docker
    #docker build --no-cache=true -t test -f Dockerfile.test ./
    #docker save test > ./test.tar
    #docker load < test.tar
    #docker push test
    #docker pull test
    #docker commit 1825ebfd3d19 test0
    #docker tag test testtest
    #docker run -dit --name test0 test /bin/bash
    #docker stop test0 && docker rm test0

    #RUN mv libcossdk.a.bak libcossdk.a
    #RUN g++ -I/usr/local/include -I/usr/include -I/usr/local/curl -L./ -std=c++14 -w -o test test.cpp ./libcossdk.a -lpthread -ldl 
    #RUN rm -f Dockerfile.* *.a *.h *.cpp *.o *.out docker.sh build.sh
    #CMD ["./test"]
    
    CMD ["python3 test.py"]
    ####################################################################################

#su_id

    su - root
    su -
    sudo -i root 

    id root
    id -g root

    groups test

    cat /etc/passwd | less
    cat /etc/group    

    adduser test

    usermod -u new_uid username
    ::用户 username 的GID修改为 new_gid
    usermod -g new_gid username

    chown user filename
    chgrp groupname filename

    chmod
    400 -r--------   拥有者能够读 其他任何人不能进行任何操作；
    644 -rw-r--r--   拥有者都能够读，但只有拥有者可以编辑；
    666 -rw-rw-rw-   所有用户都有文件读、写权限
    755 -rwxr-xr-x   所有人都能读和执行，但只有拥有者才能编辑；
    777 -rwxrwxrwx   所有人都能读、写和执行（该设置通常不是好想法）。
    文件 664 目录 775     chmod 400 密钥文件

#python3

    apt-get install python3
    apt-get install python3-pip
    //////////////////////////////
    or windows
    python3 -m pip install --upgrade pip --force-reinstall
    //////////////////////////////
    pip3 install --upgrade pip

    pip3 install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
    pip3 install numpy -i http://mirrors.aliyun.com/pypi/simple/

#kernel

    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-headers-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-headers-6.3.0-060300_6.3.0-060300.202304232030_all.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-image-unsigned-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-modules-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    sudo dpkg --install *.deb
    sudo reboot
    uname -r

#rhel9
    //dnf yum
    1、挂载系统光盘到/mnt/cdrom目录
    mkdir -p /mnt/cdrom
    mount /dev/sr0 /mnt/cdrom

    2、设置系统启动后将光盘自动挂载到/mnt/cdrom
    echo "/dev/sr0 /mnt/cdrom iso9660 defaults 0 0" >> /etc/fstabcat /etc/fstab

    3、切换到/etc/yum.repos.d/目录
    cd /etc/yum.repos.d/
    vim RHEL8.repo #vim redhat.repo #RHEL9

    4、按下i键，输入以下内容
    [BaseOS]
    name=BaseOS
    baseurl=file:///mnt/cdrom/BaseOS
    enabled=1
    gpgcheck=0
    [AppStream]
    name=AppStream
    baseurl=file:///mnt/cdrom/AppStream
    enabled=1
    gpgcheck=0

    5、测试Yum配置是否可用
    yum -y install nginx或者dnf -y install nginx
    yum install net-tools -y

    ----rhel7
    mount -o loop /rhel-server-7.9-x86_64-dev.iso /mnt/cdrom
    [base]
    name=base
    baseurl=file:///mnt/cdrom
    enabled=1
    gpgcheck=0
    -------------------------------------
    //SELINUX
    /usr/sbin/sestatus -v

    修改/etc/selinux/config 文件
    将SELINUX=enforcing改为SELINUX=disabled
    reboot

    systemctl stop firewalld
    systemctl disable firewalld
    systemctl status firewalld  //Active: inactive (dead) 关闭

    service iptables stop
    chkconfig iptables off
    systemctl status iptables.service

#llm
    
    //Langchain-Chatchat
    sudo apt install python3-pip
    python3 -m pip install --upgrade pip
    wget https://repo.anaconda.com/archive/Anaconda3-2024.02-1-Linux-x86_64.sh
    bash Anaconda3-2024.02-1-Linux-x86_64.sh
    vim ~/.bashrc
    export PATH="~/anaconda3/bin":$PATH
    source ~/anaconda3/bin/activate
    >source ~/.bashrc

    conda update -n base conda #update最新版本的conda
    #source activate #conda deactivate
    conda create -n langchain python==3.11.7
    conda activate langchain
    conda deactivate langchain
    conda remove --name langchain --all
    conda env list
    conda info --envs

    git clone https://github.com/chatchat-space/Langchain-Chatchat.git
    cd Langchain-Chatchat
    python3 -m pip install -r requirements.txt
    python3 -m pip install -r requirements_api.txt
    python3 -m pip install -r requirements_webui.txt

    sudo apt-get install git-lfs
    git lfs install
    $ git clone https://huggingface.co/THUDM/chatglm3-6b
    $ git clone https://huggingface.co/BAAI/bge-large-zh

    python copy_config_example.py

    vim configs/model_config.py
    MODEL_PATH = {
        "embed_model": {
            "bge-large-zh": "/data/BAAI/bge-large-zh",
            "m3e-base": "/data/datasets/m3e-base"}, 
        "llm_model": {
            "chatglm2-6b": "/data/THUDM/chatglm2-6b",
            "chatglm3-6b": "/data/THUDM/chatglm3-6b",
    }}
    python init_database.py --recreate-vs

    #startup.py "cpu" model_config.py "cpu" server_config.py "cpu"
    python startup.py -a
    http://127.0.0.1:8501

//////

    //llm ollama langchain streamlit webui RAG
    curl https://ollama.ai/install.sh | sh
    //localhost:11434
    ollama serve
    ollama run llama2
    ollama pull llama2:13b
    ollama run codellama
    ollama run llama2:70b
    ollama run qwen:14b
    ollama pull mixtral:8x7b
    ollama run llama2-chinese
    ollama run mistral

    Downloaded from Hugging Face https://huggingface.co/TheBloke/finance-LLM-GGUF/tree/main FROM "./finance-llm-13b.Q4_K_M.gguf" PARAMETER temperature 0.001 PARAMETER top_k 20 TEMPLATE """ {{.Prompt}} """ # set the system message SYSTEM """ You are Warren Buffet. Answer as Buffet only, and do so in short sentences. """
    ollama create arjunrao87/financellm -f Modelfile

    sudo systemctl status ollama
    pip3 install langchain
    //docker run -d -v ollama:/root/.ollama -p 11434:11434 --name ollama ollama/ollama
    //docker exec -it ollama ollama run <參數>
    //docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v ollama-webui:/app/backend/data --name ollama-webui --restart always ghcr.io/ollama-webui/ollama-webui:main
    //docker run -d --network=host -v open-webui:/app/backend/data -e OLLAMA_BASE_URL=http://127.0.0.1:11434 --name open-webui --restart always ghcr.io/open-webui/open-webui:main
    docker run -d -p 3000:8080 -e OLLAMA_API_BASE_URL=http://your_host:11434/api -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main
    ollama run llama2-chinese "天空为什么是蓝色的？"

    pip3 install chromadb langchain BeautifulSoup4 gpt4all langchainhub pypdf chainlit

    启动之后可以访问 http://localhost:8080

//////

    //huggingface
    git lfs install
    huggingface-cli login
    git init ./
    git clone https://huggingface.co/google/gemma-7b-it
    git remote add origin 'huggingface.co/google/gemma-1.1-7b-it'
    git remote set-url origin https://cisxin:hf_xxxxxxxxxxxxxxxxxxxxxxxxxxxx@huggingface.co/google/gemma-1.1-7b-it
    GIT_SSL_NO_VERIFY=1 git clone https://huggingface.co/google/gemma-1.1-7b-it
    huggingface-cli download --resume-download gemma-1.1-7b-it --local-dir gemma-1.1-7b-it

    git pull origin

    

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

#java　.keystore

    //生成密钥对
    /home/tomcat /jdk1.8.0_201/bin/keytool -genkey -alias tomcat -keyalg RSA -keySize 4096 -sigAlg SHA256withRSA -keystore /home/tomcat/ccttpp2/apache-tomcat-9.0.44/conf/cert/ssffiitt.keystore

    //导入云端证书
    keytool -import -v -file /home/tomcat/ctp2/apache-tomcat-9.0.44/conf/cert/202011.cer -keystore /home/tomcat/ccttpp2/apache-tomcat-9.0.43/conf/cert/ssffiitt.keystore

