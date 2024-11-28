# contents

- [contents](#contents)
- [grep](#grep)
- [awk](#awk)
- [sed](#sed)
- [ps crontab](#ps-crontab)
- [vim](#vim)
- [ssh su id](#ssh-su-id)
- [for while](#for-while)
- [git](#git)
- [g++ python3 go](#g-python3-go)
- [samba rsync](#samba-rsync)
- [mac os](#mac-os)
- [disk fsck ln](#disk-fsck-ln)
- [network route nftable netsh tcpdump](#network-route-nftable-netsh-tcpdump)
- [curl nc ab pptpsetup](#curl-nc-ab-pptpsetup)
- [docker kvm](#docker-kvm)
- [LaTeX Σ](#latex-σ)
- [llm](#llm)
- [kernel 时区 rhel9](#kernel-时区-rhel9)
- [jenkins fs](#jenkins-fs)
- [iis \[java .keystore\] elk](#iis-java-keystore-elk)

# grep

当前目录包含字符串文件

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
    2.次数匹配
        * 匹配前面的(紧挨着的)字符0次到n次
        ？匹配前面的字符0次到1次
        + 匹配前面的字符1次到n次
        \{m\} 匹配前面的字符m次   例：a\{7\}   aaaaaaa
        \{m,n\} 匹配前面的字符m到n次 
        \{0,n\} 匹配前面的字符0到n次
        \{m,\} 匹配前面的字符至少m次
      grep "[0-9]\{2,3\}" /etc/passwd
      grep "[0-9]\+" /etc/passwd
    3.位置锚定 
      ^锚定行首
      $锚定行尾    

# awk

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
    sudo tar -zcvf gitlab20240516.tar.gz /srv/gitlab
    tar -xzvf gitlab20240516.tar.gz

# sed

    sed -n 's/0.0.0.0/1.1.1.1/p' a.txt
    sed -i 's/107.25.6.7/172.20.20.1/g' *.xml
    sed -i 's/1\.1\.1\.1/0\.0\.0\.0/g' a.txt
    sed -i "s/qh0/$qh0/g" `grep -rl 'qh0' --include="*.sh" --include="*.conf" --include="*.yml" --exclude="*.bash" ./`
    sed '/%local_ip_v4_v4%/d' $(pwd)/template/vars.xml > $(pwd)/conf/vars.xml
    sed '/%local_ip_v4_v4%/s/.*/ /' $(pwd)/template/vars.xml > $(pwd)/conf/vars.xml

    替换\r\n->\n
    sed -i "s/\r//g" file_name
    find /home/test -name "*.sh" | xargs dos2unix
    dos2unix file_name

    vim c.txt //replace one line
    export LANG=en_US.UTF-8
    sed -i.bak '/^export LANG=/c\export LANG=zh_CN.utf8' c.txt

    //unicode to utf-8
    file -i s.txt
    iconv -f utf-16 -t utf-8 s.txt > s2.txt
    iconv -f GBK -t UTF-8 seg.txt -o seg.txt.utf8  //ISO-8859
    cat ko.txt | iconv -f GBK -t UTF-8
    echo -e "\u4f1a\u8bdd\u8fc7\u671f\uff0c\u8bf7\u91cd\u65b0\u767b\u9646"  //会话过期，请重新登陆 //printf "\u4f1a\n"

    
# ps crontab
    ps -ef | grep msgbak | grep -v  grep | awk '{print $2}' | xargs kill -9

    bg fg jobs ctr+z nohup

    ls -l --time-style=+"%Y-%m-%dT%H:%M:%S" | grep 2023-08 | awk '{print $7}' | xargs ls -lah
    ls -l --time-style=+%Y%m%d | awk '{if (length($6) > 0 && $6 < 20230808) {print $7}}' | xargs -r ls 
    ls -lah -Str

    cd .. && cd - && cd ~

  删除100天前文件

    find ./xml_cdr -mtime +100 -type f -name *.xml -exec rm -rf {} \;

    //mv所有media下2天前文件
    find /app/media -type f -mtime +2 |xargs -i mv -f {} /app/media.bak
    
    sudo mv -f /app/msg/`date -d "2 days ago" +%Y%m%d`.txt /app//msg.bak/

    -------------------------
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

  不可见字符

    grep --color='auto' -P -n "[^\x00-\x7F]" file.xml
    sed 's/\x0b/ /g' b.txt > c.txt

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

    //rm "deleted file"
    sudo lsof -v | grep deleted
    java       3138                 root    1w      REG              253,1 78145200669     393809 /root/nohup.out (deleted)
    ls -l /proc/3138/fd/*
    l-wx------ 1 root root 64 Jul 23 09:36 /proc/3138/fd/1 -> /root/nohup.out (deleted)
    echo > /proc/3138/fd/1
    ls -l /proc/$(ps -ef | grep -E "java -jar Saa.*\.jar" | grep -v "color=auto" | awk '{print $2}')/fd/1
    
  //crontab

    etc/init.d/crond status
    sudo systemctl status cron.service

    59 23 * * * (cd /app; bash test.sh)
    10 01 * * 6 (sh /app/0.sh; sh /app/1.sh)

    
# vim

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
    echo -e -n "\x63\x69\x73\x0a" > aaaa.txt
    
    :vim gbk utf-8
    vim ~/.vimrc
    let &termencoding=&encoding
    set fileencodings=utf-8,gb18030,gb2312,gbk,big5
    set number

# ssh su id

    ssh -i "test.pem" ubuntu@xxxx.cn-north-1.compute.amazonaws.com.cn
    scp -i
    scp -P port 01.py user@remote:Desktop/01.py
    scp -P port user@remote:Desktop/01.py 01.py
    scp -r demo user@remote:Desktop
    scp -r user@remote:Desktop demo
    shpass -p "123456" scp -P port 01.py user@remote:Desktop/01.py

    //Port 40022
    sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
    sudo sed -i 's/#Port 22/Port 40022/' /etc/ssh/sshd_config
    cat /etc/ssh/sshd_config | grep 40022
    sudo systemctl restart ssh
    sudo reboot
    //在 ~/.ssh 目录下生成密钥对文件
    ssh-keygen -t rsa
    //ssh-keygen -m PEM -t rsa -b 4096 //生成./id_rsa id_rsa.pub
    //复制(192.168.0.x)id_rsa.pub到主机(202.x.x.x)上指定用户的 ~/.ssh/authorized_keys
    cat id_rsa.pub >> authorized_keys //本地客户机 id_rsa.pub,远程服务器 authorized_keys
    sudo vim /etc/ssh/sshd_config  //PasswordAuthentication yes, PubkeyAuthentication yes 可以同时启用
      PubkeyAuthentication yes
    sudo systemctl restart ssh //reboot
    ++++++++
    //多个SSH密钥 vim ~/.ssh/config
    Host remote_host
        HostName 10.10.0.94
        User test
        IdentityFile ~/.ssh/id_rsa2
    //使用配置的别名进行连接：ssh remote_host
    --------
    //chmod 600 .ssh/authorized_keys #注意权限！//server
    //~/.ssh/known_hosts 文件存储了远程主机的公钥 //编辑文件更新已知主机的公钥信息 //client 
    //第一次连接到某个远程服务器,SSH客户端会收到该远程服务器的公钥=>known_hosts//防止中间人攻击(MITM攻击)
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

    //限制用户(包括root用户)连续输入错误密码的次数。在3次失败锁定 锁定10分钟,之后自动解锁
    sudo vim /etc/pam.d/sshd 或 /etc/pam.d/login
    auth required pam_faillock.so deny=3 unlock_time=600 even_deny_root root_unlock_time=600

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
    
    //OpenSSH私钥keyname,转换为keyname.ppk
    sudo apt-get install putty-tools
    puttygen id_rsa -o id_rsa_putty.ppk

  // su id

    su - root
    su - //会话的环境变量,路径等会被切换到root用户的配置
    sudo -i root 

    echo "$password" | sudo -S your_command
    echo "123456" | sudo -S apt update

    id root
    id -g root

    groups test

    cat /etc/passwd | less
    cat /etc/group    

    adduser test

    usermod -u new_uid username
    ::用户 username 的GID修改为 new_gid
    usermod -g new_gid username

    chown user filename //chown user:group filename
    chgrp groupname filename

    chmod
    400 -r--------     拥有者能够读 其他任何人不能进行任何操作；
    644 -rw-r--r--     拥有者都能够读，但只有拥有者可以编辑；
    666 -rw-rw-rw-     所有用户都有文件读、写权限
    755 -rwxr-xr-x     所有人都能读和执行，但只有拥有者才能编辑；
    777 -rwxrwxrwx     所有人都能读、写和执行（该设置通常不是好想法）。
    文件 664 目录 775   chmod 400 密钥文件
    
    //simple password
    sudo passwd test

# for while

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

  //while

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

# git

    git show 显示最后一次的文件改变的具体内容
    git show -5 显示最后 5 次的文件改变的具体内容
    git branch -a 查询远程分支

    撤消本地修改 git fetch --all && git reset --hard origin/master //git reset --hard origin/main
    服务器ip变更: git remote set-url origin http://10.10.0.110:10080/test/test.git
    本地缺文件 git checkout    git checkout aaaa.ini 
    将提交重置 git reset --hard HEAD
    查看全局配置 git config -l
    工程本目录查看.gitignore文件    全局gitignore

    //提交和检出代码时均不进行转换
    git config --global core.autocrlf false
    vscode->file->preferences->settings->profile files.eol \n

    //切到master
    git pull
    //解决冲突（解决master的冲突，保证代码最新）
    git add .
    git merge --continue //(git update-ref -d MERGE_HEAD)
    git commit . -m "message"
    git merge develop
    //解决冲突（解决两个分支的冲突，实现合并）
    git add  .
    git merge --continue //(git update-ref -d MERGE_HEAD) 
    git commit . -m "message"
    git push origin master

    git log --name-status 每次修改的文件列表, 显示状态
    git log --name-only 每次修改的文件列表
    git log --stat 每次修改的文件列表, 及文件修改的统计
    git whatchanged 每次修改的文件列表
    git whatchanged --stat 每次修改的文件列表, 统计
    git show commitid 显示某个 commitid 改变的具体内容
    git diff HEAD^ # 比较与上一个版本的差异
    git remote -v //url

    git config --local credential.helper store 保存用户名密码
    //or
    [credential]
      helper = store    

    回退到某个版本
    git checkout 7cd0386bd67e7f240b55fd037159ff7fe8f7063b seg_sensitive.txt

    git remote set-url origin https://ghp_xxxxxxxx@github.com/username/test.git
    
    中断 继续
    git clone --recursive https://huggingface.co/THUDM/chatglm3-6b

    git reset --hard 15b0718d2e0f08e58cb24f73dc82be7420a76149 //回滚到以前的提交
    git push --force origin master //强制推送到远程分支
    git checkout main //切换到其他分支
    git branch -D temp-branch //强制删除分支
    git push --force origin main //继续推送
    
    ////////////////////
    第四步：Github账号上添加公钥
    进入Settings设置 -> SSH and GPG keys -> New SSH key - > 复制id_rsa.pub内容 -> 粘贴保存
    第五步：验证是否设置成功
    $ ssh -T git@github.com
     successfully authenticated //表明设置成功
    不需要账号密码clone和push 注意:使用ssh的url

    cd /tmp/jack && echo $(pwd) && git pull http://test:123456@github.com:10080/jack.git

  docker pull gitlab/gitlab-ce:latest

    docker commit ce35cab8103b gitlab/gitlab-ce:12.0.3-ce.0
    docker save gitlab/gitlab-ce:12.0.3-ce.0 > ./gitlab20240516gitlab_ce12.0.3-ce.0.tar
    =>
    docker load < gitlab20240516gitlab_ce12.0.3-ce.0.tar
    sudo bash rungitlab.sh && docker exec -it gitlab0 chown git /var/opt/gitlab/.ssh/authorized_keys && docker exec -it gitlab0 chmod 2770 /var/opt/gitlab/git-data/repositories

    //修改服务器的IP地址
    sudo vim gitlab/config/gitlab.rb
        external_url "http://10.10.0.110:10080"

  ////gitlab error

    error ts=2024-01-28T06:34:20.553201665Z caller=main.go:717 err="opening storage failed: block dir: \"/var/opt/gitlab/prometheus/data/01HHJ3Y17S5YZBD6B449RAKRYK\": 1.1.unexpected end of JSON input"
    2.cd /var/opt/gitlab/
    3.sudo systemctl stop gitlab-runsvdir
    4.sudo gitlab-ctl stop 
    5.sudo rm-rf /var/opt/gitlab/prometheus/data/ 
    6.sudo rm /var/opt/gitlab/gitaly/gitaly.pid 
    7.sudo rm ./redis/dump.rdb -rf 
    8.sudo systemctl restart gitlab-runsvdir 
    9.sudo gitlab-ctl restart

  //gitlab-ce
  
    docker run -dti -v $(pwd)/config:/etc/gitlab -v $(pwd)/logs:/var/log/gitlab -v $(pwd)/data:/var/opt/gitlab -p 80:80 -p 443:443 -p 10022:22 -e external_url='http://localhost' -e gitlab_rails['gitlab_shell_ssh_port']=10022 --name gitlab0 docker.awsl9527.cn/gitlab/gitlab-ce:latest

    docker exec -it gitlab0 /bin/bash
    gitlab-rails console -e production
    user = User.where(id:1).first
    #<User id:1 @root>
    # input
    user.password='123456'
    # 保存
    user.save!
    # 退出
    exit

    vim /etc/gitlab/gitlab.rb
    gitlab-rails console
    gitlab-ctl reconfigure
    #gitlab-ctl restart
    qq邮箱设置->第三方服务->IMAP/SMTP服务 开启
    gitlab_rails['smtp_enable'] = true
    gitlab_rails['smtp_address'] = "smtp.qq.com"
    gitlab_rails['smtp_port'] = 587
    gitlab_rails['smtp_user_name'] = "xxxx@qq.com"
    gitlab_rails['smtp_password'] = "lkjxxxxxxxxxxxx"
    gitlab_rails['smtp_domain'] = "smtp.qq.com"
    gitlab_rails['smtp_authentication'] = "login"
    gitlab_rails['smtp_enable_starttls_auto'] = true
    gitlab_rails['smtp_tls'] = false
    #gitlab_rails['smtp_openssl_verify_mode'] = 'peer'
    gitlab_rails['gitlab_email_from'] = "xxxx@qq.com"
    gitlab_rails['gitlab_email_reply_to'] = "xxxx@qq.com"
    gitlab-rails console
    Notify.test_email('xxxx@qq.com','mail title','mail txt').deliver_now

    定时备份 vim /etc/gitlab/gitlab.rb
    gitlab_rails['manage_backup_path'] = true
    gitlab_rails['backup_path'] = "/var/opt/gitlab/backups"  #gitlab默认备份目录 +777
    gitlab_rails['backup_archive_permissions'] = 0644  #生成的备份文件权限
    gitlab_rails['backup_keep_time'] = 604800  #默认备份保留天数为7天
    //【0 0 * * 7 /root/backup.sh > /root/backup.log 2>&1】
    gitlab-rake gitlab:backup:create > /dev/null 2>&1 //gitlab-rake gitlab:backup:create --trace
    if [ $? -ne 0 ];then
        echo 'Backup gitlab data error!' > /home/gitlab/backup_gitlab.log

    vim /etc/gitlab/gitlab.rb
    external_url 'https://your-domain.com'  //??
    nginx['redirect_http_to_https'] = true    
    nginx['ssl_certificate'] = "/etc/gitlab/ssl/server.crt"
    nginx['ssl_certificate_key'] = "/etc/gitlab/ssl/server.key"

    docker cp your_certificate.crt gitlab:/etc/gitlab/ssl/gitlab.crt
    docker cp your_private_key.key gitlab:/etc/gitlab/ssl/gitlab.key

# g++ python3 go

    sudo vim /etc/profile
    export LD_LIBRARY_PATH=/usr/local/lib:${LD_LIBRARY_PATH}
    source /etc/profile

    sudo apt-get install pkg-config automake autoconf libtool libssl-dev libcrypto++-dev libcurl4-gnutls-dev libzip-dev libevent-dev libmicrohttpd-dev
    //libssl-dev libboost-all-dev libevent-dev libboost-test-dev zlib1g-dev libjsoncpp-dev libminizip-dev
    sudo apt-get install mono-devel //debian  
    sudo apt-get install libpoco-dev libmysqlclient-dev

  //gdb

    strings libXX.so | grep GCC
    echo | g++ -dM -E -x c++ - | grep __cplusplus

    sudo apt-get install sbcl clisp

    sudo add-apt-repository ppa:swi-prolog/stable
    sudo apt-get update
    sudo apt-get install swi-prolog

    sudo apt-get install libboost-all-dev

    nm libcossdk.a | grep MultiUploadObject

  //python3

    apt-get install python3
    apt-get install python3-pip

    //24.04
    sudo apt-get install python3-all-dev
    pip3 install numpy --break-system-packages
    sudo apt-get install python3-numpy python3-scipy python3-configobj python3-sympy python3-mpmath python3-functoolsplus python3-matplotlib
    //pipx install xxxx

    //////////////////////////////
    or windows
    python3 -m pip install --upgrade pip --force-reinstall
    //////////////////////////////
    pip3 install --upgrade pip
    pip3 install pipreqs
    pipreqs /path/to/your/project
    pipreqs /path/to/your/project --force    

    pip3 install numpy -i https://pypi.tuna.tsinghua.edu.cn/simple
    pip3 install numpy -i http://mirrors.aliyun.com/pypi/simple/
    pip install jax[cuda12-pip] -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com
    pip install jaxlib==0.4.25+cuda12.cudnn89  -i http://mirrors.aliyun.com/pypi/simple/ --trusted-host mirrors.aliyun.com

  //go

    sudo apt-get install golang-go
    go env
    go version
    //$GOROOT //install path //$GOPATH //search path for importing packages //export GOBIN=$GOROOT/bin/
    go env -w GOPROXY=https://goproxy.cn

    //debug
    一、假设你的golang项目代码是在vscode终端以go run main.go -e dev来启动的，那么打开用vscode打开项目目录
    二、在VSCode的侧边栏中，点击调试图标（虫子图标）打开调试视图。
    三、点击调试视图顶部的"create a launch.json file"链接。这将创建一个名为launch.json的文件，用于配置调试任务。
    四、在launch.json中，找到并修改 "configurations" 部分，
    添加以下配置示例：(如果是go run main.go运行这个项目的，不需要最后一个arg参数):
    {
        // Use IntelliSense to learn about possible attributes.
        // Hover to view descriptions of existing attributes.
        // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
        "version": "0.2.0",
        "configurations": [],

        "name": "Launch",
        "type": "go",
        "request": "launch",
        "mode": "debug",
        "program": "${workspaceFolder}/main.go",
        "args": ["-e", "dev"]
            
    }
    五、设置断点
    六、启动调试 (install dlv)

  //vue

    sudo apt install npm
    sudo npm install vue-cli -g
    sudo npm install -g @vue/cli
    npm install @vue/cli-plugin-babel --save-dev
    npm install -g serve
    serve -s dist
    vue -V
    vue list
    vue init webpack project-name
        npm run build:dev
    npm run serve //npm run serve -- --port 8080
    npm run build     npm run dev
    vue-cli-service build --mode development 
    //opensslErrorStack: [ 'error:03000086:digital envelope routines::initialization error' ],
    export NODE_OPTIONS=--openssl-legacy-provider
    //set NODE_OPTIONS=--openssl-legacy-provider && npm run build:dev
    cd usr/share/nginx/html/    //ls /var/www/html
                +-------------------------------------+
                |                View                 |
                |                  ↓                  |
                |                DOM                  |
                +-------------------------------------+
                              |         ↑
                              v         |
    +---------------------------------------------------------+
    |                      ViewModel                          |
    |  +-----------------------------+  +------------------+  |
    |  |        DOM Listeners        |  |    Data Bindings  |  |
    |  +-----------------------------+  +------------------+  |
    +---------------------------------------------------------+
                              |         ^
                              v         |
                +-------------------------------------+
                |               Model                 |
                |                  ↓                  |
                |      Plain JavaScript Objects       |
                +-------------------------------------+

    +---------------------------+
    |           构建            |
    |  +---------------------+  |
    |  |        状态         |  |
    |  |  +---------------+  |  |
    |  |  |     路由      |  |  |
    |  |  |  +---------+  |  |  |
    |  |  |  |  组件   |  |  |  |
    |  |  |  | +-----+ |  |  |  |
    |  |  |  | | 视图 | |  |  |  |
    |  |  |  | +-----+ |  |  |  |
    |  |  |  +---------+  |  |  |
    |  |  +---------------+  |  |
    |  +---------------------+  |
    +---------------------------+

  //vscode

    extensions
    Partial Diff  //select tow files 

    vscode->file->preferences->settings->profile files.eol \n
    vscode->file->preferences->settings->profile editor.autoClosingBrackets=never
    vscode->file->preferences->settings->profile editor.autoClosingQuotes=never

    "Search" input ">hex editor"

  //rust

    curl --proto '=https' --tlsv1.2 https://sh.rustup.rs -sSf | sh
    rustc --version
    rustup update
    cargo --version && cargo new testrust
    cargo build && cargo run && cargo check
    VSCode + Rust-Analyzer, LLDB DAP, CodeLLDB,=>main.rs => run|debug
  
# samba rsync

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

  //rsync

    rsync -av -e 'ssh -p 2234' source/ user@remote_host:/destination

    //--link-dest参数指定基准目录/compare/path，然后源目录/source/path跟基准目录进行比较，找出变动的文件，将它们拷贝到目标目录/target/path。那些没变动的文件则会生成硬链接。这个命令的第一次备份时是全量备份，后面就都是增量备份了。
    rsync -a --delete --link-dest /compare/path /source/path /target/path

    sudo apt-get install sshpass
    sshpass -p "123456" rsync -av --delete /tmp/aaaa test@192.168.0.1:/tmp
    sshpass -p "123456" rsync -av --progress --delete /tmp/aaaa test@192.168.0.1:/tmp >> /tmp/log.txt 2>&1
    echo `date` "rsync test end ....." >> /tmp/log.txt
    sshpass -p "123456" ssh  test@10.1.0.15 "docker exec -i freeswitch0 fs_cli -x 'show channels as json'"
    sshpass -p '123456' ssh -o StrictHostKeyChecking=no john@192.168.1.100 'docker ps'

# mac os

    locate tf-keras-datasets
    sudo /usr/libexec/locate.updatedb

    /Users/test/Library /Users/test/Library/Containers
    /Users/test/Library/Developer /Users/test/Library/Caches
    /Applications /Library

    //ubuntu plocate updatedb

    vim /etc/security/limits.conf
    # 用户  jdoe 最大可以打开 1024个文件
    jdoe    soft    nofile    1024
    jdoe    hard    nofile    2048
    # 所有用户  最多可以创建 2048个进程
    *       soft    nproc     1024
    *       hard    nproc     2048
    # 用户组 wheel 最大可以打开 4096 个文件
    @wheel  soft    nofile    4096
    @wheel  hard    nofile    8192
    # 用户 foo 可以锁定 64MB 的内存
    foo     soft    memlock   65536
    foo     hard    memlock   65536

  //占用1GB内存1个小时
  
    #!/bin/bash
    mkdir /tmp/memory
    mount -t tmpfs -o size=1024M tmpfs /tmp/memory
    dd if=/dev/zero of=/tmp/memory/block
    sleep 3600
    rm /tmp/memory/block
    umount /tmp/memory
    rmdir /tmp/memory

  //os
    //禁止休眠:
    sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target

    last -x|grep shutdown|head -1  //查看最后一次关机时间
    journalctl -xe  //查看最新的系统日志
    /var/log/syslog //包含系统日志信息
    /var/log/dmesg  //包含开机时的日志信息 
    sudo dmesg | grep something  //Linux内核的消息缓冲器
    logger "Hello World"  //写入系统日志
    journalctl -u sshd.service  //单独显示某个服务的日志
    last  //登录日志
    lastlog  //每个用户上次登录的时间 "/etc/log/lastlog"

  //apt udpate

    sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup
    sudo vim /etc/apt/sources.list
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble-updates main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble-backports main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble-security main restricted universe multiverse
    deb https://mirrors.tuna.tsinghua.edu.cn/ubuntu/ noble-proposed main restricted universe multiverse
    #Codename: noble

    1.noble main restricted universe multiverse
        main：官方完全支持的软件，包含大多数常用的免费软件。
        restricted：包含了非自由软件，但这些软件得到了 Ubuntu 开发团队的支持。
        universe：社区维护的软件包，可能没有得到官方支持，但由开源社区提供。
        multiverse：不自由的软件包，这些软件包可能受到版权或其他法律限制。
    2. noble-updates main restricted universe multiverse
        这个分支包含了已经发布的 Ubuntu 版本的稳定更新，包括安全补丁和修复程序。这些更新包已经过测试，可以确保系统的稳定性和安全性。
    3. noble-backports main restricted universe multiverse
        这个分支包含了从未来的 Ubuntu 版本中挑选出来并移植到当前版本的软件包。通常情况下，这些软件包是为了满足用户对新特性或新软件的需求。
    4. noble-security main restricted universe multiverse
        这个分支专门用于安全更新，提供了对系统中发现的安全漏洞的修复。这些更新包的发布速度较快，旨在尽快解决安全问题。
    5. noble-proposed main restricted universe multiverse
        这个分支包含了尚未完全测试和验证的更新和新软件包。这些更新包可能还在测试阶段，供开发者和高级用户提前体验和反馈。

  //windows命令窗口中文乱码

    chcp 65001
    chcp 936
    
    //service name:service_name2
    sc create service_name2 binPath= "D:\service_name2.exe"
    sc create service_name binPath= "C:\service_name.exe"
    sc delete service_name 

    磁盘(D:)-属性-共享-高级共享-共享此文件夹 //\\10.10.0.1

    powercfg -h off

# disk fsck ln

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

    2.添加新LVM到已有的LVM组，实现扩容
    sudo lvm                //进入lvm管理
    lvm>pvcreate /dev/sda3  //这是初始化刚才的分区，必须的(将物理分区创建为物理卷)(in linux lvm(ext4)) //默认安装sda1 sda2 sda3存在
    lvm>vgextend ubuntu-vg /dev/sda3  //将初始化过的分区加入到虚拟卷组ubuntu-vg (卷和卷组的命令可以通过 vgdisplay, vgextend ubuntu-vg /dev/sda3)
    lvm>vgdisplay -v
    lvm>lvextend -l+953861 /dev/mapper/ubuntu--vg-ubuntu--lv //扩展已有卷的容量(953861 通过vgdisplay查看的Free PE / Size的大小) //redhat:lvextend -l+100%FREE /dev/mapper/rhel-opt 
    lvm>pvdisplay                      //查看卷容量，这时你会看到一个很大的卷了
    lvm>quit                           //退出
    //lvdisplay /dev/mapper/rhel-opt

    3.以上只是卷扩容了，下面是文件系统的真正扩容，输入以下命令：
    sudo lvdisplay /dev/mapper/ubuntu--vg-ubuntu--lv
    sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv (系统安装后，扩容默认大小)
    //redhat:xfs_growfs /dev/mapper/rhel-opt

    4.查看新的磁盘空间
    df -h
    //lvm 调整大小
    sudo vgdisplay
    lvextend -L 120G /dev/mapper/ubuntu--vg-ubuntu--lv //增大至120G
    resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv        //执行调整
    df -h

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

  disk速度测试

    sudo time dd if=/dev/zero of=test.dat bs=8k count=130000
    sudo time dd if=/dev/sda of=/dev/null bs=8k count=130000
    sudo time dd if=/dev/sda of=testrw.dat bs=8k  count=130000

  ubuntu disk 4TB

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

  // fsck

    fsck /dev/mapper/ubuntu--vg-ubuntu--lv
    fsck /dev/sda1
    fsck -fvy /
    chkdsk c: /f
    sudo mount -o remount,rw /partition/identifier /mount/point
    sudo mount -o remount, rw /dev/mapper/ubuntu--vg-ubuntu--lv /
    mount -v | grep "^/" | awk '{print "\nPartition identifier: " $1  "\n Mountpoint: "  $3}'

  // ln

    ln -s s->t
    ln -s ../bin/python3.8 /usr/local/bin/python3
    mklink /d C:\.nuget E:\.nuget    

# network route nftable netsh tcpdump

    sudo apt install net-tools

    ip link show
    sudo ip link set eno4 up
    sudo ip link set eno4 down

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
        eno4:
          addresses: [10.1.0.216/16,10.1.0.213/16]
          gateway4: 10.1.0.1
          nameservers:
            addresses:
            - 10.1.0.1
            search: []            
      version: 2

    sudo vim /etc/systemd/resolved.conf
    DNS=114.114.114.114 8.8.8.8
    sudo systemctl restart systemd-resolved

    //iperf:
    TCP服务端命令：iperf -s -i 1 -p 3389
    TCP客户端命令：iperf -c 172.19.16.97 -p 3389 -i 1

    UDP服务端命令:iperf -u -s -i 1 -p 3389
    UDP客户端命令:
    1、iperf -u -c 172.19.16.97 -p 3389 -b 1500M -i 1
    2、iperf -u -c 172.19.16.97 -p 3389 -b 2000M -i 1

    //映射共享目录
    mount -t cifs //192.168.0.1/temp /tmp/temp -o username=test,password=123456
    umount /tmp/temp

    //查看系统tcp连接中各个状态的连接数
    netstat -an | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
    //查看和本机80端口建立连接并状态在established的所有ip
    netstat -an |grep 80 |grep ESTA |awk '{print$5 "\n"}' |awk 'BEGIN {FS=":"} {print $1 "\n"}' |sort |uniq    
    //输出每个ip的连接数，以及总的各个状态的连接数。
    netstat -n | awk '/^tcp/ {n=split($(NF-1),array,":");if(n<=2)++S[array[(1)]];else++S[array[(4)]];++s[$NF];++N} END {for(a in S){printf("%-20s %s\n", a, S[a]);++I}printf("%-20s %s\n","TOTAL_IP",I);for(a in s) printf("%-20s %s\n",a, s[a]);printf("%-20s %s\n","TOTAL_LINK",N);}'

  //route

    sudo route add -net 58.33.x.x netmask 255.255.255.255 ens40
    sudo route add -net 10.0.0.0 netmask 255.0.0.0 gw 10.40.0.95 br0
    sudo route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.40.0.95 br0
    sudo route add -net 0.0.0.0 netmask 0.0.0.0 gw 10.40.0.1 ens40
    sudo route del -net 14.18.x.x netmask 255.255.255.255
    sudo route del -net 0.0.0.0 netmask 0.0.0.0 br0
    sudo route del -net 0.0.0.0 netmask 0.0.0.0 ens192
    sudo route del -net 0.0.0.0 netmask 255.0.0.0 gw 10.40.0.95 br0
    route add -host 10.1.0.214 gw 10.1.0.216
    route add -net 10.1.0.214 netmask 255.255.255.255 gw yz216
    sudo route add -net 172.20.20.0 netmask 255.255.255.0 gw 192.168.88.1 ens192

    ip route add 10.10.0.0 via 10.40.0.1
    ip route del 10.10.0.0/16
    -------------------------------------------------------?????????
    sudo route add -net 10.1.0.215/16 gw 10.10.14.96

    traceroute 121.37.x.x
    
    //win
    route delete 0.0.0.0  mask 0.0.0.0 192.168.225.1
    route -p add 10.10.0.0 mask 255.255.0.0 10.40.86.105
    route -p add 10.1.0.0 mask 255.255.0.0 192.168.1.5
    route -p add 10.10.0.0 mask 255.255.0.0 192.168.1.5
    route -p add 10.1.0.0 mask 255.255.0.0 10.23.14.96
    route add 10.50.0.0 mask 255.255.0.0 192.168.0.234

  //nftable nft

    sudo nft list tables
    sudo nft list chains
    sudo nft list ruleset
    sudo nft add table inet t8080
    #在filter表中创建一个名为input的链,类型为filter,挂钩点(hook)为input,优先级为0
    sudo nft add chain inet t8080 t8080a { type filter hook input priority 0 \; }
    #接受来自IP地址 1.116.81.x 且目标端口为8080的TCP数据包
    sudo nft add rule inet t8080 t8080a ip saddr 1.116.81.x tcp dport 8080 accept
    sudo nft add rule inet t8080 t8080a ip saddr 1.116.81.x udp dport 8080 accept
    sudo nft add rule inet t8080 t8080a ip saddr 10.23.8.1/16 tcp dport 8080 accept
    #拒绝所有目标端口为8080的TCP流量(除非它们已经被前面的规则接受)拒绝所有其他目标端口为8080的TCP流量
    sudo nft add rule inet t8080 t8080a tcp dport 8080 reject
    sudo bash -c "nft list ruleset > /etc/nftables.conf"
    sudo systemctl restart nftables.service # nft -f /etc/nftables.conf
    sudo nft list ruleset
    /////
      chain INPUT {
        type filter hook input priority filter; policy accept;
        meta l4proto tcp tcp dport 8080 counter packets 0 bytes 0 drop
      }
    }
    table inet t8080 {
      chain t8080a {
        type filter hook input priority filter; policy accept;
        ip saddr 1.116.81.0 tcp dport 8080 accept
        ip saddr 10.23.0.0/16 tcp dport 8080 accept
        tcp dport 8080 reject
        ip saddr 1.116.81.0 udp dport 8080 accept 
        udp dport 8080 reject        
      }

    /////

  //windows netsh

    1. 查询端口映射情况
    netsh interface portproxy show v4tov4
    2. 查询某一个IP的所有端口映射情况
    netsh interface portproxy show v4tov4 | find "[IP]"
    例：netsh interface portproxy show v4tov4 | find "192.168.1.1"
    3. 增加一个端口映射
    netsh interface portproxy add v4tov4 listenaddress=[外网IP] listenport=[外网端口] connectaddress=[内网IP] connectport=[内网端口]
    例：netsh interface portproxy add v4tov4 listenaddress=192.168.5.239 listenport=8080 connectaddress=192.168.1.50 connectport=80
    录音文件下载端口转发：
    netsh interface portproxy add v4tov4 listenaddress=192.168.5.239 listenport=8080 connectaddress=120.x.x.x connectport=2024
    //netsh interface portproxy add v4tov4 listenaddress=10.10.0.107 listenport=8080 connectaddress=140.x.x.x connectport=2024
    4. 删除一个端口映射
    netsh interface portproxy delete v4tov4 listenaddress=[外网IP] listenport=[外网端口]
    例：netsh interface portproxy delete v4tov4 listenaddress=2.2.2.2 listenport=8080
    //netsh interface portproxy delete v4tov4 listenaddress=192.168.5.239 listenport=8080

  //tcpdump

    tcpdump -i enp3s0 host 10.1.0.x -v
    tcpdump -ni ens160 tcp and host 10.0.0.1 and port 8080 -v
    tcpdump -ni enp3s0 udp and host 10.1 -v -w /tmp/1.cap
    tcpdump -ni enp3s0 udp and host 121.37.x.x -v -w /tmp/1.cap

    sudo tcpdump -i eno1 -A -s 0 'tcp port 11000 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'
    sudo tcpdump -s 0 -i eth0 -A '(tcp dst port 11000 and tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420) or (tcp dst port 11000 and (tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x504f5354))'
    
# curl nc ab pptpsetup

    curl -H "Content-Type: application/json" -X POST -d '{"name":"test", "Company_name":"testtest", "mobile":"10086","status":1, "msg":"OK!" }' "http://10.1.1.5:8080/v1/api/insertdocument"
    curl --referer "tiga.ss888.com" "https://www.test.com/1616985544896.mp3" --output d:\1.mp3
    curl --referer "www.ss888.com" "https://www.test.com/1616985544896.mp3" --output d:\1.mp3
    curl --referer "0.0.0.21" "https://www.test.com/1616985544896.mp3" --output d:\1.mp3

    ab -n 1000 -c 1000 "http://10.1.1.5:8080/v1/api/getdocument"
    ab -n400 -c20  -p "img.json" -T "application/x-www-form-urlencoded" "http://10.1.1.5:8080/v1/api/getdocument"

    ftp ftp://test:123456@10.10.0.2:21 => put *.tar => mput * => mput *.tar

  //nc

    nc -l 127.0.0.1 5300
    nc -u -l -p 8080
    echo "hello udp!" | nc -u 10.1.0.x 8080
    while true; do nc -l 5300; done

  //pptpsetup

    sudo pptpsetup --create testvpn --server 10.10.15.100 --username vpn --password vpn --encrypt --start
    sudo route del -net 10.10.8.240 netmask 255.255.255.255
    sudo route add -net 10.3.0.0 netmask 255.255.0.0 dev ppp0
    sudo pptpsetup --create testvpn --server 10.10.15.100 --username vpn --password vpn --encrypt --start && sudo route del -net 10.23.8.240 netmask 255.255.255.255 && sudo route add -net 10.3.0.0 netmask 255.255.0.0 dev ppp0
    sudo vim /etc/ppp/options     

# docker kvm

    sudo docker ps -a | grep "Exited" | awk '{print $1}'| xargs docker stop
    sudo docker ps -a | grep "Exited" | awk '{print $1}'| xargs docker rm
    sudo docker images | grep none | awk '{print $3}'| xargs docker rmi

    //docker install
    curl -fsSL  https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add
    sudo add-apt-repository "deb [arch=amd64]  https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" 
    sudo apt-get update
    sudo apt-get install docker-ce
    sudo usermod -aG ${USER} or sudo usermod -aG docker $USER

    //ubuntu24.04
    sudo apt-get remove snap
    sudo apt update
    sudo apt install apt-transport-https ca-certificates curl gnupg2 software-properties-common
    curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
    sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
    sudo apt update
    sudo apt-get install docker-ce docker-ce-cli containerd.io
    sudo mkdir -p /etc/docker
    sudo tee /etc/docker/daemon.json <<-'EOF'
    {
      "registry-mirrors": ["https://ung2thfc.mirror.aliyuncs.com"]
    }
    EOF
    sudo systemctl daemon-reload && sudo systemctl restart docker
  
  //clean logs file

    #!/bin/sh
    echo 123456 | sudo su << "EOF"
    logs=$(find /var/lib/docker/containers/ -name *-json.log)  
    for log in $logs  
    do 
        echo `date` "clean logs : $log" >> /tmp/cleanlog.txt
        cat /dev/null > $log
        echo `date` "clean logs : $log"    
    done
    exit

    docker ps -aq | xargs docker inspect --format='{{.LogPath}}' | xargs truncate -s 0

  //docker build
  
    #cd docker 
    #docker build --no-cache=true -t test -f Dockerfile.test ./
    #docker save test:latest > ./test.tar
    #docker load < test.tar
    #docker push test
    #docker pull test
    #docker commit 1825ebfd3d19 test0
    #docker tag test testtest
    #docker run -dit --name test0 test /bin/bash //--log-opt max-size=10m --log-opt max-file=3
    #docker stop test0 && docker rm test0
    du -d1 -h /var/lib/docker/containers | sort -rh

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

    #RUN mv libcossdk.a.bak libcossdk.a
    #RUN g++ -I/usr/local/include -I/usr/include -I/usr/local/curl -L./ -std=c++14 -w -o test test.cpp ./libcossdk.a -lpthread -ldl 
    #RUN rm -f Dockerfile.* *.a *.h *.cpp *.o *.out docker.sh build.sh
    #CMD ["./test"]
    
    docker pull registry.baidubce.com/paddlepaddle/paddle:2.4.1
    sudo tee /etc/docker/daemon.json <<EOF
      {
        "registry-mirrors": [
            "https://hub.uuuadc.top",
            "https://docker.anyhub.us.kg",
            "https://dockerhub.jobcher.com",
            "https://dockerhub.icu",
            "https://docker.ckyl.me",
            "https://docker.awsl9527.cn"
        ]
      }
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    docker pull docker.awsl9527.cn/gitlab/gitlab-ce

    docker login -u test123456 -p cis 192.168.0.1:5000
    docker tag debian 192.168.0.1:5000/debian
    docker push 192.168.0.1:5000/debian
    docker logout 192.168.0.1:5000

    FROM python:3.12-bookworm
    ENV TZ Asia/Shanghai
    ENV PYTHONIOENCODING utf-8
    RUN apt-get update -y \
        && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
        && apt-get install -y tzdata && apt-get clean && apt-get autoclean 
    RUN apt-get install -y vim net-tools git wget curl
    RUN pip3 install pycryptodome PyJWT rsa Flask Flask_Caching gunicorn requests Flask-RESTful Flask-SQLAlchemy Flask-Cors flask-redis cos_python_sdk_v5 Flask_SocketIO eventlet grpcio redis SQLAlchemy waitress pymysql jinja2 xlwt elasticsearch elasticsearch-dsl Flask-APScheduler pyparsing
    docker build --no-cache=false -t python:3.12a -f Dockerfile .
    FROM python:3.12a 
    ENV TigaEnv production
    ADD . /code
    WORKDIR /code
    RUN pip3 install -i https://mirrors.aliyun.com/pypi/simple/ --upgrade -q pip 
    CMD ["waitress-serve", "--threads", "4", "--port", "5050", "--call", "factory:create_app"]
    EXPOSE 5050
    docker run -dit --name flask0 python:3.12a

  //registry

    sudo apt-get install apache2-utils
    htpasswd -Bc ./passwords test
    123456

    sudo vim /etc/docker/daemon.json  //docker login 是客户端发出
      "insecure-registries": ["10.0.0.110:5000"] 
    sudo systemctl restart docker
    
    docker run -d -p 5000:5000 \
        -e REGISTRY_AUTH=htpasswd \
        -e REGISTRY_AUTH_HTPASSWD_REALM=Registry \
        -e REGISTRY_AUTH_HTPASSWD_PATH=/etc/docker/registry/passwords \
        -v $(pwd)/passwords:/etc/docker/registry/passwords -v "$(pwd)/data":/data \
        --name registry0 registry
    docker login -u test -p '123456' http://10.0.0.110:5000  //or jenkins0 bash

  //#docker fix ip

    docker stop nginxtest && docker rm nginxtest
    docker network rm staticnet
    docker network create --subnet=192.168.100.1/24 staticnet
    docker network ls
    docker run -itd --name nginxtest --net staticnet --ip 192.168.100.44 nginx
    ping 192.168.100.44
    telnet 192.168.100.44 80
    telnet 10.60.0.175 80
    sudo iptables -t filter -L
    sudo iptables -L -n --line-number
    sudo iptables -t nat -L -n -v --line-number
    sudo iptables -t nat -A OUTPUT -s 192.168.100.44 -j DNAT --to-destination 10.60.0.175
    sudo iptables -t nat -A PREROUTING -d 10.60.0.175 -j DNAT --to-destination 192.168.100.44
    sudo iptables -t nat -D OUTPUT 2
    sudo iptables -t nat -D PREROUTING 2
    sudo iptables -t nat -D POSTROUTING 4

  //uninstall

     sudo systemctl stop docker
     sudo apt-get purge docker-ce docker-ce-cli containerd.io
     sudo rm -rf /var/lib/docker && sudo rm -rf /var/lib/containerd
     sudo rm -rf /var/lib/docker && sudo rm -rf /etc/docker
     sudo userdel docker && sudo groupdel docker
     sudo rm -rf /var/run/docker.sock && sudo rm -rf /var/run/docker.pid
     //Docker的其他依赖或扩展,也可以通过apt进行清除
     sudo apt-get autoremove --purge docker-ce docker-ce-cli containerd.io

     //docker stop容器失败
     docker network disconnect --force bridge 容器id

  //docker update

    curl -fsSL https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
    sudo vim /etc/apt/sources.list.d/docker.list
    deb [arch=amd64] https://mirrors.tuna.tsinghua.edu.cn/docker-ce/linux/ubuntu noble stable

    sudo apt-key list
    pub rsa4096 2020-01-29 [SC]
    8CAE 012E BFAC 38B1 7A93  7CD8 C5E2 2450 0C12 89C0
    复制ID的后8位
    sudo apt-key export 0EBFCD88 | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/pgdg.gpg

  //kvm

    //install
    egrep -o '(vmx|svm)' /proc/cpuinfo
    lsmod | grep kvm
    sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst virt-manager 

    ++++++++ set bridges
    sudo systemctl start libvirtd.service
    sudo systemctl enable libvirtd.service
    sudo service libvirtd status
    -------
    sudo cat /etc/netplan/01-netcfg.yaml 
    # This file describes the network interfaces available on your system
    # # For more information, see netplan(5).
    network:
      version: 2
      renderer: networkd
      ethernets:
        ens33:
          dhcp4: no
          dhcp6: no
      bridges:
        br0:
          interfaces: [ens33]
          dhcp4: no
          optional: true
          addresses: [10.40.50.237/16]
          gateway4: 10.40.0.1
          nameservers:
            addresses: [114.114.114.114,10.40.0.1]
    -----
    sudo netplan apply
    ifconfig

    br0:.....

    //没权限会导致dhcp分配失败
    sudo iptables -A FORWARD -p all -i br0 -j ACCEPT

    sudo networkctl status -a
    brctl show
    --------

    //vnc ok
    sudo virt-install --name=virt0 --memory=4096,maxmemory=4096 --vcpus=2,maxvcpus=4 \
    --virt-type kvm --install ubuntu18.04 --os-type=linux --os-variant=ubuntu18.04 \
    --location 'http://archive.ubuntu.com/ubuntu/dists/bionic/main/installer-amd64/' \
    --disk path=/tmp/vm/virt0.img,size=512 \
    --boot cdrom,hd --cdrom /tmp/ubuntu-18.04.4-live-server-amd64.iso \
    --network bridge:br0 --graphics vnc,listen=0.0.0.0 --noautoconsole -v  
    
    //to operate
    virsh shutdown virt0 & virsh destroy virt0 & virsh undefine virt0 & rm -rf /tmp/vm/*  

    virsh list --all
    virsh start virt0
    virsh shutdown virt0

    virsh console virt0

    virsh autostart virt0 //开机自启动虚拟机,生成配置文件/etc/libvirt/qemu/autostart/vm-node1.xml
    virsh autostart --disable virt0 //取消开机自启动

    sudo virt-clone -o virt0 -n database_devel -f /path/to/virt01.img 

# LaTeX Σ

  $H(s) = \frac{Y(s)}{X(s)}$
  $$H(z) = \frac{Y(z)}{X(z)}$$
  
  // Σ

    Α	α	alpha
    Β	β	beta
    Γ	γ	gamma
    Δ	δ	delta
    Ε	ε	epsilon
    Ζ	ζ	zeta
    Η	η	eta
    Θ	θ	theta
    Ι	ι	iota
    Κ	κ	kappa
    Λ	λ	lambda
    Μ	μ	mu
    Ν	ν	nu
    Ξ	ξ	xi
    Ο	ο	omicron
    Π	π	pi
    Ρ	ρ	rho
    Σ	σ	sigma
    Τ	τ	tau
    Υ	υ	upsilon
    Φ	φ	phi
    Χ	χ	chi
    Ψ	ψ	psi
    Ω	ω	omega

    名称     音标                 大写   小写  名称      音标                           大写   小写
    alpha    /'ælfə/              Α     α     nu       /nju:/                         Ν      ν
    beta     /'bi:tə/ /'beɪtə/    Β     β     xi       希腊 /ksi/ 英美/ˈzaɪ/ /ˈksaɪ/   Ξ      ξ
    gamma    /'gæmə/              Γ     γ     omicron  /əuˈmaikrən/ /ˈɑmɪˌkrɑn/       Ο      ο
    delta    /'deltə/             Δ     δ     pi       /paɪ/                          Π      π
    epsilon  /'epsɪlɒn/           Ε     ε     rho      /rəʊ/                          Ρ      ρ
    zeta     /'zi:tə/             Ζ     ζ     sigma    /'sɪɡmə/                       Σ      σ, ς
    eta      /'i:tə/              Η     η     tau      /tɔ:/ 或 /taʊ/                 Τ      τ
    theta    /'θi:tə/             Θ     θ     upsilon  /ˈipsilon/ 或  /ˈʌpsɨlɒn/      Υ      υ
    iota     /aɪ'əʊtə/            Ι     ι     phi      /faɪ/                          Φ      φ
    kappa    /'kæpə/              Κ     κ     chi      /kaɪ/                          Χ      χ
    lambda   /'læmdə/             Λ     λ     psi      /psaɪ/                         Ψ      ψ
    mu       /mju:/               Μ     μ     omega    /'əʊmɪɡə/ /oʊ'meɡə/            Ω      ω

# llm

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

  //ollama

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

  //huggingface
  
    git lfs install
    huggingface-cli login
    git init ./
    git clone https://huggingface.co/google/gemma-7b-it
    git remote add origin 'huggingface.co/google/gemma-1.1-7b-it'
    git remote set-url origin https://cis:hf_xxxxxxxxxxxxxxxxxxxxxxxxxxxx@huggingface.co/google/gemma-1.1-7b-it
    GIT_SSL_NO_VERIFY=1 git clone https://huggingface.co/google/gemma-1.1-7b-it
    huggingface-cli download --resume-download gemma-1.1-7b-it --local-dir gemma-1.1-7b-it

    git pull origin    

# kernel 时区 rhel9

    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-headers-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-headers-6.3.0-060300_6.3.0-060300.202304232030_all.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-image-unsigned-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    wget https://kernel.ubuntu.com/~kernel-ppa/mainline/v6.3/amd64/linux-modules-6.3.0-060300-generic_6.3.0-060300.202304232030_amd64.deb
    sudo dpkg --install *.deb
    sudo reboot
    uname -r

  //时区

    sudo tzselect //sudo timedatectl set-timezone Asia/Shanghai && timedatectl
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
    LANG="en_US.utf8"
    LANGUAGE="en_US.utf8:"
    LC_TIME="en_DK.UTF-8"
    
  //rhel9

    //dnf yum
    1.挂载系统光盘到/mnt/cdrom目录
    mkdir -p /mnt/cdrom
    mount /dev/sr0 /mnt/cdrom
    2.设置系统启动后将光盘自动挂载到/mnt/cdrom
    echo "/dev/sr0 /mnt/cdrom iso9660 defaults 0 0" >> /etc/fstabcat /etc/fstab
    3.切换到/etc/yum.repos.d/目录
    cd /etc/yum.repos.d/
    vim RHEL8.repo #vim redhat.repo #RHEL9
    4.按下i键,输入以下内容
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
    ----------------------
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

# jenkins fs

    crontab  */15
                                   *      *      *        *        *
    含义                           分钟   小时   日期     月份     星期
    取值范围                       0-59   0-23   1月31日  1月12日  0-6
    示例                                                        
    每隔15分钟执行一次              H/15   *      *        *        *   
    每隔2个小时执行一次             H      H/2    *        *        *     
    每隔3天执行一次                 H      H      H/3      *        *     
    每隔3天执行一次(每月的1-15号)    H      H      1-15/3   *        *     
    每周1,3,5执行一次               H      H      *        *        1,3,5     
                                                                        
    指定时间范围   a-b                                         
    指定时间间隔   /                                           
    指定变量取值   a,b,c                                       
    H/15: H HASH,随机均匀分布
  
  docker build --no-cache=true -t jenkins_docker -f Dockerfile ./

    FROM jenkins/jenkins:lts
    USER root
    RUN apt-get update && apt-get -y install apt-transport-https ca-certificates curl gnupg2 software-properties-common && \
        curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg > /tmp/dkey; apt-key add /tmp/dkey && \
        add-apt-repository \
          "deb [arch=amd64] https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
          $(lsb_release -cs) \
          stable" && \
        apt-get update && apt-get -y update && apt-get -y install docker-ce
    USER jenkins

    docker stop jenkins0
    docker rm jenkins0
    docker run -d --name jenkins0 -e "HOSTDIR=$PWD" -p 9081:8080 -p 50000:50000 -v /var/run/docker.sock:/var/run/docker.sock -v $(which docker):/usr/bin/docker -v $PWD/jenkins_home2:/var/jenkins_home --memory=8g --env JAVA_OPTS="-Xms1024m -Xmx1024m -XX:MaxNewSize=1024m" jenkins0

  //删除项目

    "Manage Jenkins"(管理 Jenkins)->"Script Console"
    def jobNamesToDelete = ["job1", "job2", "job3"] // 替换为要删除的项目名称列表
    jobNamesToDelete.each { jobName ->
        def job = Jenkins.instance.getItem(jobName)
        if (job) {
            job.delete()
            println("Deleted job: ${jobName}")
        } else {
            println("Job not found: ${jobName}")
        }
    }  
    删除历史数据: rm -rf JENKINS_HOME/jobs/<Job Name>/builds/xxxx
    intstall publive over ssh
    系统管理->系统配置->Publish over SSH
    project->configure->configure->add building step->configure->"Send files or execute commands over SSH"->add "SSH Server"
    源码管理->git->Repository URL->Credentials->jenkins->username with password             Branches to build->*/main

    https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json

    //Build Steps,Runs a shell script
    #!/bin/bash
    IMAGE_NAME="testsync"

    IMAGE_SERVER="10.0.0.110:5000"
    EXISTING_TAGS=$(docker images --format "{{.Repository}}:{{.Tag}}" | grep "$IMAGE_NAME:v")
    MAX_VERSION=0
    if [ -n "$EXISTING_TAGS" ]; then
        for TAG in $EXISTING_TAGS; do
            VERSION=$(echo $TAG | sed "s/^$IMAGE_SERVER\/$IMAGE_NAME:v//")
            if [[ "$VERSION" =~ ^[0-9]+$ ]]; then
                if [ "$VERSION" -gt "$MAX_VERSION" ]; then
                    MAX_VERSION=$VERSION
                fi
            fi
        done
        NEW_VERSION=$((MAX_VERSION + 1))
    else
        NEW_VERSION=0
    fi
    docker build --no-cache=false -f Dockerfile -t $IMAGE_NAME .
    docker tag $IMAGE_NAME $IMAGE_SERVER/$IMAGE_NAME:v$NEW_VERSION
    docker tag $IMAGE_NAME $IMAGE_SERVER/$IMAGE_NAME:latest
    docker push $IMAGE_SERVER/$IMAGE_NAME:v$NEW_VERSION
    docker push $IMAGE_SERVER/$IMAGE_NAME:latest
    docker rmi $IMAGE_NAME

  //fs

    //build
    cd freeswitch/docker/master
    docker build --no-cache=true --build-arg TOKEN=pat_1X8EQXH6EvgajWaBVWSJCG51 -t freeswitch -f Dockerfile ./
    cd freeswitch\conf\vanilla  //通用的配置

    //fix
    originate sofia/gateway/route  /17fix  #17ims#fs  #9904#68888888#68888888#013800138000 &park()
    originate sofia/gateway/gw214/17216#17213#215#9904#68888888#68888888#013800138000 &park()
    originate sofia/gateway/gw68888888/013800138000 &echo()

    originate user/9002 &bridge(user/9005)
    originate sofia/gateway/gw68888888/013800138000 &bridge(user/9005)
    originate user/9005 &bridge(sofia/gateway/gw68888888/13800138000)
    originate user/9907 9901 xml default
    originate user/9905 &ivr(menu_0001)

    uuid_bridge 032d6115-f206-4507-b2a6-4f2e38bddae3 5985787c-c696-498d-baac-17bcfe2d2c89
    originate {sip_h_from=<sip:68888888@10.10.78.18>}sofia/gateway/gw68888888/013800138000 &park()

    //originate sofia/internal/1769#17215#113#9005#68888888#68888888#13800138000@10.10.0.21 &park()

    docker pull image.xxxx.com/freeswitch
    docker run -d -v /app/fs/freeswitch_config:/etc/freeswitch -v /app/fs/tmp:/tmp -v /app/fs/record:/data/record -v /app/fs/sounds:/data/sounds -v /dev/shm:/var/lib/freeswitch/db -v /etc/localtime:/etc/localtime:ro -v /app/fs/tmp/log:/var/log --net=host --name freeswitch0 -it image.xxxx.com/freeswitch
    docker exec -it freeswitch0 fs_cli -x "sofia status profile internal reg" | grep 2337
    docker exec -it freeswitch0 fs_cli -x "sofia profile external killgw gw223"
    docker exec -it freeswitch0 fs_cli -x "sofia profile external2 rescan"
    docker exec -it freeswitch0 fs_cli -x "reloadxml"
    docker exec -it freeswitch0 fs_cli -x "sofia status"
    docker exec -it freeswitch0 fs_cli -x "show channels"
    docker exec -it freeswitch0 fs_cli -x "global_getvar local_ip_v4"

    show registrations
    sofia profile internal siptrace on
    sofia profile internal siptrace off

    //设置sip head
    originate {sip_from_uri=68888888@10.10.78.18}{sip_invite_to_uri=013800138000@10.10.78.18:5060}sofia/gateway/gw68888888/013800138000@10.10.254.81 &park()
    originate {absolute_codec_string=PCMA,PCMU,OPUS}sofia/gateway/gw68888888/013800138000 &bridge(user/9004)

    EXPOSE 8021/tcp
    EXPOSE 5060/tcp 5060/udp 5080/tcp 5080/udp
    EXPOSE 5061/tcp 5061/udp 5081/tcp 5081/udp
    EXPOSE 7443/tcp
    EXPOSE 5070/udp 5070/tcp
    EXPOSE 64535-65535/udp
    EXPOSE 16384-32768/udp
    //tcp:8021 5060 5080 5061 5081 7443 5070
    //udp:5060 5080 5061 5081 5070 64535-65535 16384-32768

    sudo apt install sngrep
    sudo sngrep port 5060
    sudo sngrep host 192.168.1.100

# iis [java .keystore] elk

    // 导出所有应用程序池
    C:\Windows\System32\inetsrv\appcmd.exe list apppool /config /xml > d:\temp\apppools.xml
    // 导入所有应用程序池
    C:\Windows\System32\inetsrv\appcmd.exe add apppool /in < d:\temp\apppools.xml
    // 导出所有站点
    C:\Windows\System32\inetsrv\appcmd.exe list site /config /xml > d:\temp\sites.xml
    // 导入所有站点
    C:\Windows\System32\inetsrv\appcmd.exe add site /in < d:\temp\sites.xml
    // 导出单独的应用程序池
    C:\Windows\System32\inetsrv\appcmd.exe list apppool "应用程序池名称" /config /xml > c:myapppool.xml
    # 导入单独的应用程序池
    C:\Windows\System32\inetsrv\appcmd.exe add apppool /in < c:myapppool.xml
    // 导出单独站点
    C:\Windows\System32\inetsrv\appcmd.exe list site "站点名称" /config /xml > c:mywebsite.xml
    // 导入单独站点
    C:\Windows\System32\inetsrv\appcmd.exe add site /in < c:mywebsite.xml

    --explicitly-allowed-ports=10080,9801

    https://docs.python.org/3/
    https://en.cppreference.com/w/    //zh

    √√√╳╳╳
    C:\Users\Administrator\.config\clash-verge
    C:\Users\Administrator\.config\clash-verge\config.yaml

    腾讯云->账号中心->项目管理
    域名申请->ssl证书申请->DNS验证(CNAME)->ssl证书验证确认->云解析DNS/CLB  ... SSL证书-主动配置到->CLB CLB->选择SSL证书
    腾讯云->域名注册->我的域名->域名注册
    腾讯云->域名注册->我的域名->域名注册->解析=>云解析 DNS->我的解析->ss888.com->记录管理->添加记录->www/@ 0.0.0.179(重庆1)
    腾讯云->ICP备案->我的备案->新增/接入服务
    腾讯云->SSL证书->我的证书->购买证书/上传证书->部署    证书验证->证书验证(确认)
    DNS:腾讯云->负载均衡->实例管理->"实例"->监听器管理->HTTP/HTTPS监听器(已配置1个)->"-"->'+'->"ss888.cn"->已绑定后端服务->D/名称->IP地址  (域名解析到一个腾讯云的负载均衡实例上)
    腾讯云->云解析DNS->我的解析->"ss888.com"->解析(新手快速解析)->负载均衡(付费) （可解析到非腾讯云服务器）(DNS验证:特定邮箱,CNAME,网站控制权验证/txt文件验证)
    腾讯云->访问管理->访问密钥->API密钥管理  (API 请求的重要凭证)
    腾讯云->云数据库 TencentDB->MySQL->数据库备份
    腾讯云->容器镜像服务->镜像仓库->广州->python/快捷指令->ss888/python:3.12a
    腾讯云->云解析DNS->我的解析->记录管理->"test.ss888.com"->A->0.0.0.179(CLB实例)
    腾讯云->负载均衡->实例管理->"实例"->监听器管理->HTTP/HTTPS监听器(已配置2个)->"-"->'+'->"tiga.ss888.com"->已绑定后端服务->ID/名称->IP地址 (0.0.0.200/172.17.0.1:6001, 0.0.0.41/172.17.0.11:6001)
    腾讯云->负载均衡 ... -> HTTPS监听器 ... -> 添加规则(选证书,其它默认) ... -> 绑定 -> 可选多个端口

    JS接口安全域名 微信公众号上配置安全域名  https://域名/MP_verify_*.txt    public/MP_verify_*.txt
    微信公众平台官网中的「设置与开发」-「功能设置」-「网页授权域名」
    在公众平台网站的高级功能-开发模式页，点击"成为开发者"按钮 -> 开发者帐号 -> 微信公众号的APPID(开发者ID) "设置与开发"中的"基本设置"
    公众号的AppID、小程序的AppID、开放平台的AppID、第三方平台的AppID、移动应用的AppID、网站应用的AppID、小商店的AppID

    //nginx
    docker run -dit -p 80:80 -p 443:443 -p 8020:8020 -v /tmp/html:/usr/share/nginx/html -v /tmp/nginx/config:/etc/nginx --name nginx0 nginx
    FROM nginx
    COPY ./dist /usr/share/nginx/html

    sudo vim /etc/nginx/conf.d/website.conf
    server {
        listen       80;
        server_name  test.vip www.test.vip test.cn www.test.cn;
        rewrite ^(.*)$ https://${server_name}$1 permanent;
    }
    server {
      listen 443 ssl;
      server_name test.vip www.test.vip;

      ssl_certificate /home/ubuntu/ssl/STAR_test_vip/Nginx/STAR_test_vip.pem;
      ssl_certificate_key /home/ubuntu/ssl/STAR_test_vip/Nginx/STAR_test_vip.key;
      ssl_session_cache shared:SSL:1m;
      ssl_session_timeout  10m;

      root /home/ubuntu/website;
      location / {
        index index.html;
      }
    }
    server {
      listen 443 ssl;
      server_name test.cn www.test.cn;

      ssl_certificate /home/ubuntu/ssl/STAR_test_cn/Nginx/STAR_test_cn.pem;
      ssl_certificate_key /home/ubuntu/ssl/STAR_test_cn/Nginx/STAR_test_cn.key;
      ssl_session_cache shared:SSL:1m;
      ssl_session_timeout  10m;

      root /home/ubuntu/website;
      location / {
        index index.html;
      }
    }
    sudo nginx -t && sudo nginx -s reload
    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;

        location /pg/app {
            proxy_pass https://api.xxxx.com;
            proxy_set_header Host $proxy_host;
            #proxy_set_header X-Real-IP $remote_addr;
            #proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            #proxy_set_header X-Forwarded-Proto $scheme;
        }

        location /appapi {
            proxy_pass https://vms-test.xxxx.com;
            proxy_set_header Host $proxy_host;
        }
    }

    proxy_cache_path /tmp/nginx/cache keys_zone=one:10m loader_threshold=300 loader_files=200 max_size=200m;
    upstream lightningTmp {
        server 10.3.0.120:11000 weight=5;
    }
    server {
        listen 11000;
        server_name 10.3.0.73;
        proxy_cache one;
        location / {
            #client_body_buffer_size      500M;
        
            #proxy_cache STATIC;
            proxy_cache_key $host$uri$is_args$args;
            proxy_cache_valid any 10m;
            proxy_cache_min_uses 3;
            proxy_cache_bypass $cookie_nocache $arg_nocache$arg_comment;
            proxy_read_timeout 300;
            proxy_connect_timeout 300;
            proxy_redirect off;
            
            proxy_set_header X-Forwarded-Proto $scheme;
            proxy_set_header Host  $http_host;
            proxy_set_header X-Real-IP  $remote_addr;
            
            rewrite /v4/(.*)$ /$1 break;
            
            proxy_pass http://10.3.0.120:11000;
            expires 1d;
        }   
    }       

    upstream lightning {
        server 10.3.0.57:11000 weight=5;
        server 10.3.0.57:11000 weight=10;
    }
    server {
        listen 8020;
        server_name 10.32.0.59;
        location ~* /v4/ {
            #rewrite ^/v4/(.*)$ http://10.3.0.57:11000/$1 break;
            rewrite /v4/(.*)$ /$1 break;
            proxy_pass http://lightning;
        }
    }

  //java .keystore

    //生成密钥对
    /home/tomcat /jdk1.8.0_201/bin/keytool -genkey -alias tomcat -keyalg RSA -keySize 4096 -sigAlg SHA256withRSA -keystore /home/tomcat/ccttpp2/apache-tomcat-9.0.44/conf/cert/ssffiitt.keystore

    //导入云端证书
    keytool -import -v -file /home/tomcat/ctp2/apache-tomcat-9.0.44/conf/cert/202011.cer -keystore /home/tomcat/ccttpp2/apache-tomcat-9.0.43/conf/cert/ssffiitt.keystore

    wget https://download.oracle.com/java/21/latest/jdk-21_linux-x64_bin.rpm
    rpm -ivh jdk-21_linux-x64_bin.rpm
    sudo dpkg -i jdk-21_linux-x64_bin.deb

  //mysql

    mkdir -p ./data ./logs ./conf ./conf/conf.d ./conf/mysql.conf.d
    docker pull mysql:5.7
    vi ./conf/my.cnf
    [client]
    default-character-set=utf8mb4

    [mysql]
    default-character-set=utf8mb4

    [mysqld]
    init_connect="SET collation_connection = utf8mb4_unicode_ci"
    init_connect="SET NAMES utf8mb4"
    character-set-server=utf8mb4
    collation-server=utf8mb4_unicode_ci
    skip-character-set-client-handshake
    skip-name-resolve

    docker run -p 3306:3306 --name mysql0 -v $PWD/log:/var/log/mysql -v $PWD/data:/var/lib/mysql -v $PWD/conf:/etc/mysql --restart=always -e MYSQL_ROOT_PASSWORD=123456 -dit mysql:5.7

    docker exec -ti  mysql0 mysql -u root -p
    SET PASSWORD FOR 'root'@'%' = PASSWORD('123456');
    //ALTER USER 'root'@'%' IDENTIFIED BY '123456';
    FLUSH PRIVILEGES;
    exit;
    
    //mysql9:
    mkdir -p ./data ./logs ./conf ./conf/conf.d ./conf/mysql.conf.d
    docker stop mysql20 && docker rm mysql20
    chmod 644 /etc/mysql/my.cnf
    vi ./conf/my.cnf
    [client]
    default-character-set=utf8mb4

    [mysql]
    default-character-set=utf8mb4

    [mysqld]
    init_connect="SET collation_connection = utf8mb4_unicode_ci"
    init_connect="SET NAMES utf8mb4"
    character-set-server=utf8mb4
    collation-server=utf8mb4_unicode_ci
    docker run -p 3307:3306 -v $PWD/log:/var/log/mysq -v $PWD/data:/var/lib/mysql -v $PWD/conf:/etc/mysql --restart=always -e MYSQL_ROOT_PASSWORD=123456 --name mysql20 -dit mysql:latest
    docker exec -ti  mysql20 mysql -u root -p
    ALTER USER 'root'@'%' IDENTIFIED BY '123456';
    FLUSH PRIVILEGES;

  //elk

    docker run -d -p 9200:9200 -p 9300:9300 -p 8088:8080 -v `pwd`/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -v `pwd`/data:/usr/share/elasticsearch/data -e "discovery.type=single-node" --name elasticsearch0 -t elasticsearch:7.12.0
    more elasticsearch.yml
    http.host: 0.0.0.0
    #reindex.remote.whitelist: www.test.com:9200
    ------------//install elasticsearch-analysis-ik plug-in unit
    #./elasticsearch-plugin install https://github.com/medcl/elasticsearch-analysis-ik/releases/download/v7.10.1/elasticsearch-analysis-ik-7.10.1.zip
    ------------

    docker run -d -v /app/elk/pipeline/:/usr/share/logstash/pipeline/ -v `pwd`/logstash.yml:/usr/share/logstash/config/logstash.yml -v `pwd`/ldata:/app/data -v `pwd`/logstash:/var/log/logstash  -e LS_JAVA_OPTS='-Xms2048m -Xmx2048m' -e ES_JAVA_OPTS='-Xms2048m -Xmx2048m' -p 9600:9600/tcp --name logstash0 -t docker.elastic.co/logstash/logstash:7.12.0
    vim logstash.yml
    http.host: "0.0.0.0"
    log.level: "error"
    --------------------------------
    vim pipeline/logstash.conf
    input
    { 
        elasticsearch {
            hosts => ["https://www.test.com:9200"]
            user => elastic
            password => "123456"
            index => "index_2024"
            query => '{"query": {"match_all" : {} }, "sort": ["_doc"] }'
            docinfo => true
            size => 1000
            scroll => "1m"
            codec => "json"
        }    
    }
    filter {
        mutate {
            convert => { "msgid" => "string" }
        }
    }
    output
    {
        elasticsearch
        {
            index => "index_2024"
            hosts => ["http://10.10.0.100:9200"]
            #user => elastic
            #password => "123456"
            codec => "json"
            document_id => "%{msgid}"
            action => "update"
            doc_as_upsert => true
        }
        stdout {
            codec => "json"
        }
    }
    --------------------------------

    docker run -d -p 5601:5601 -e "ELASTICSEARCH_URL=http://10.10.0.10:9200" -e "ELASTICSEARCH_HOSTS=http://10.10.0.10:9200" --name kibana0 -t kibana:7.12.0

  设置修改账号密码
  
    vim config/elasticsearch.yml
    xpack.security.enabled: true
    xpack.security.transport.ssl.enabled: true
    docker restart elasticsearch0
    //设置6个账号和密码,包括elasticsearch、kibana等
    ./bin/elasticsearch-setup-passwords interactive
    //...
    //Changed password for user [elastic]
    docker restart elasticsearch0
    
    docker exec -it kibana0 bash
    vim config/Kibana.yml
    elasticsearch.username: "elastic"
    elasticsearch.password: "123456"

    curl -k -XGET "http://10.10.0.10:9200/_cat/indices?v"
    curl -k -u 'elastic':'123456' -XPOST "http://10.10.0.10:9200/test_index_2024/_search?pretty" -H 'Content-Type: application/json; charset=UTF-8' -d'{  "query": {   "match_all": {}  } }'

  //db principle ES_AUTO_BACKUP
    PUT _snapshot/my_backup/snapshot_1?wait_for_completion=true //命令会为所有打开的索引创建名称为snapshot_1的快照
    GET _snapshot/ES_AUTO_BACKUP/_all
    GET _snapshot/ES_AUTO_BACKUP/es-o978fl2y_20240704
    POST _snapshot/ES_AUTO_BACKUP/es-o978fl2y_20240704/_restore
    {
        "indices": "message_2021"
    }
