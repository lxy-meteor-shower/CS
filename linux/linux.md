## linux

背景就不多说，为了避免忘记，本笔记从VMware开始，从头到尾，做简单介绍。

### 一、基础配置

#### 1.VMware安装和centos下载与安装

官网下载：
[VMware下载](https://www.vmware.com/products/workstation-pro/workstation-pro-evaluation.html)	(翻至下面download now)
[centOS下载](https://www.centos.org/download/)	(选X86，再随便进一个镜像网站，最后下载dvd.ios)

低版本下载：见压缩包

安装centos：
创建新的虚拟机，磁盘(看需求，可扩充，扩充方法可以搜，之前有个图形化的方法找不到了)，内存建议2G，CPU(2~4核)，dvd放上上面的ios。
打开虚拟机，选择语言，设置密码等，可以自定义磁盘分区，一般是（\root分200M，\swap与内存相符，其余的在 \上）

#### 2.终端和联网

- 添加快捷方式:（只能添加本用户的）
  setting->devices->keyboard在最底下添加

  Name：Terminal

  Command：```/usr/bin/gnome-terminal```

  key：ctrl+alt+T （与ubuntu相统一）

- 网络在右上角connect即可

#### 3.VMtools安装

1. 打开虚拟机，上方虚拟机菜单有安装VMware tools
2. 桌面出现安装包，解压
3. 安装其中的 ./vmware-install.pl
4. 一路回车
5. ```reboot```生效

#### 4.建立共享文件夹

在虚拟机设置中，右边有一个选项，选项中添加共享文件夹就可以了，共享文件夹在目录```/mnt/hgfs```下

（要在联网状态下起作用）

### 二、linux文件结构

总体架构：

![linux文件结构](.\image\系统目录结构.png)

用多了就习惯了

### 三、远程登陆linux

可以采用一些工具，比如xshell，xfpt，本文档采用windows中的powershell进行远程登录

#### 1.关闭防火墙

有时出现问题，需要关闭防火墙：
```systemctl stop firewalld```
```systemctl disable firewalld.service```

#### 2.使用powershell远程登录

- 打开powershell
  shift+右键（打开位置为打开的目录）
- 登入linux服务器
  ```ssh 名字@IP地址```
- 登出linux服务器
  ```exit（ctrl+D）```
- 用powershell登录后，右击鼠标表示粘贴

#### 3.上传下载文件

安全拷贝：```scp -r $user@host:$host:$pdir/$fname $user@host:$host:$pdir/$fname```
解释：命令 递归 出发地用户@主机： 要拷贝的文件路径/名称 目的地用户@主机：目的地路径/名称
本机上的文件可以直接写文件地址

例子：
```scp -r root@192.168.2.102:/opt/module/* root@192.168.2.104:/opt/module/``` 
在103将windows中的文件拷贝到linux中的一个例子：
```scp -r ./hello/* root@192.168.2.130:/home/```

#### 4.一些查看命令

- ```systemctl status sshd``` 查看sshd是否开启
  远程访问需要linux开启了sshd服务，该服务监听22号端口
- 查看linux的IP地址：```ifconfig```

### 四、文件编辑与开关机注销

#### 1.vi和vim编辑器

- 正常模式：
  刚进入的时候的模式，可以移动光标，复制内容（直接ctrl+c复制的那种，命令行一般使用ctrl+shift+c复制），但是不可以修改内容
- 插入模式：
  按下一些字母可以进入，一般按字母i
  可以进行文本的编辑，删除，写入等等，可以修改内容
- 命令行模式：
  在终端最底端显示
  `:wq` 表示写入并退出
  `:q` 表示退出（未修改内容时）
  `:q!` 表示强制退出（修改了内容而不需要保存时）

![示意图](.\image\vim编辑器的三种模式.png)

- 一些快捷键：
  ①在一般模式下，`yy`复制光标所在行，`5yy`复制光标所在行以及下面共五行，粘贴按p（粘贴到光标的下一行）
  ②在一般模式下，`dd`删除当前行，`5dd`删除及以下共五行
  ③查找单词，在命令行模式下，输入/单词，按回车找到光标下的第一个，再按n找到下一个（:noh取消高亮）
  ④在命令行模式下，:`set nu`出现行号	`:set nonu`不出现行号
  ⑤在一般模式下，`G`回到最后一行，`gg`回到第一行
  ⑥在一般模式下按u撤销上一次在插入模式下 的输入，再按u撤销你的撤销
  ⑦在一般模式下，先按行号，再按`shift+g`，跳转到指定行（一般在显示有行号时使用）（附：先按行号，再按回车，在和光标的基础上加那么多行）
- 其他快捷键上菜鸟教程看

#### 2.开机关机和注销

- 关机和重启：
  `sync`：同步内存到磁盘，关机前先按一下
  `shutdown -h now`：立刻关机
  `shutdown -h 1`：一分钟后关机
  `shutdown -r now`：立刻重启
  `halt`：关机
  `reboot`：重启					嫌麻烦记住后两个就行
- 用户登录和注销
  `su 用户名`：切换用户
  `logout`：注销用户（只在远程登录有效，和`exit`一样）

### 五、用户管理（root用户的权限）

![示意图](.\image\用户图解.png)

#### 1.用户

- 添加用户：
  基本语法：`useradd 用户名`（没有指定家目录，将会新建一个与用户名同名的家目录，组也是）
  `useradd -r /home/tiger/ xh`	（指定目录创建，最好是tiger没有建立）
  `useradd -g 组名 xh`    （指定组创建，组需要提前建组）
- 设置密码：
  `passwd 用户名`
  注：输入密码时看不到密码

- 删除用户：
  `userdel 用户名` （保留家目录）
  `userdel -r 用户名` （同时删除家目录）
  一般不删家目录，意思是，取消权限但保留结果
- 查询用户信息：
  `id 用户名`
  得到结果：`uid：用户id    gid：组的id    groups：哪个组`
- 切换用户：
  `su 用户名`
  注：高权限用户到低权限用户，不需要密码，反之需要密码
  `exit` 返回原目录
- 查看当前用户：`whoami`（也没什么用）

#### 2.组

用户组的存在：方便统一管理

- 增加组：`groupadd 组名`
- 删除组：`groupdel 组名`
- 修改用户组：`usermod -g 组名 用户名`

#### 3.用户和组的相关文件

- 用户配置文件（用户信息）	`/etc/passwd`
  用户名：密码：用户id：组id：注释：主目录：登录shell
- 组配置文件（组信息）			`/etc/group`
  组名：密码：组id：组用户列表
- 口令配置文件（密码和登录信息）	`/ect/shadow`

### 六、实用指令

#### 1.指定运行级别

- 运行级别说明
  3	命令行模式
  5	图形界面		
- 默认运行级别的获取与修改
  在文件`/etc/inittab`中可以看到指令
  `systemctl get-default `  获取运行级别
  `systemctl set-default multi-user.target` 命令行
  `systemctl set-default graphical.target `图形化
  注：重启后生效
- 忘记root密码（只有在服务器机才能，删库跑路还是算了）
  第一步：选择第一个按e
  ![image-20220118182745504](.\image\忘记密码1.png)
  第二步：将`ro`改成`rw` `init=sysroot/bin/bash`
  ![image-20220118183234574](.\image\忘记密码2.png)
  第三步：ctrl+x进入紧急模式
  ![image-20220118183424759](.\image\忘记密码3.png)
  第四步：`chroot /sysroot/`
  第五步：`passwd`修改密码
  第六步：`touch /.autorelabel`
  第七步：`ctrl+d（exit）`
  第八步：`reboot`
- 在遇到其他无法正常开机的情况也可以进入上述模式

#### 2.帮助指令

获取帮助信息：`man [指令或配置文件]`
enter往下翻
`help 命令` （获得shell内置命令的帮助信息）
遇到不会的指令，最好的办法是 搜

#### 3.文件目录类

| 指令    | 语法                                               | 作用                                                         | 常用参数                                                     |
| ------- | -------------------------------------------------- | :----------------------------------------------------------- | ------------------------------------------------------------ |
| pwd     | pwd                                                | 获取当前目录的绝对路径                                       | 无                                                           |
| ls      | ls [参数] [目录或文件]                             | 显示指定目录所有的文件夹和文件（默认当前目录）               | -a  包括隐藏文件<br />-l  行形式列出 (简写 ll)<br />-h 看文件大小时用 |
| cd      | cd [参数或路径]                                    | 切换目录（绝对相对都可，如cd ../../../mydir）（默认是到用户自身的家目录） | ~ 回到家目录<br />.. 回到上级目录<br />- 回到刚才的路径      |
| mkdir   | mkdir [选项] [要创建的目录]                        | 创建目录                                                     | -p 创建多级目录（指一次性创建多个）                          |
| touch   | touch 文件名 文件名 ...                            | 创建**空**文件                                               | 无                                                           |
| cp      | cp [选项] source dest                              | 拷贝文件或文件夹（cp ./* ./mydir 拷贝当前目录的所有文件到指定文件夹，*是通配符）<br />\cp 强制覆盖原来的相同文件 | -r 递归复制整个文件夹（拷贝文件夹时最好加上）<br />-f 强行复制 |
| rm      | rm [选项] 要删除的文件或目录                       | 移除文件或目录（一般用rm -rf 文件）                          | -f 强制删除不提示<br />-r 递归删除整个文件夹                 |
| mv      | mv 旧名字 新名字<br />mv 旧路径+名字 新路径+[名字] | 移动文件或目录and重命名文件                                  | 无                                                           |
| cat     | cat [选项] 要查看的文件 [\| more]                  | 查看文件内容（不能修改）                                     | -n 显示行号<br />\| more 按页显示（空格翻页）                |
| more    | more 要查看的文件                                  | 按页显示文本文件内容                                         | 快捷键见附                                                   |
| less    | less 要查看的文件                                  | 显示大型文件的内容                                           | 直接h查看快捷键                                              |
| >       | cat 文件1> 文件2<br />ls -l > 文件                 | 覆盖写入（覆盖原内容）原文件不存在会自动创建（有输出重定向的感觉） | 无                                                           |
| >>      | ls -al>> 文件<br />echo ”内容“ >> 文件(""可选)     | 追加写入（不会覆盖）原文件不存在会自动创建（有输出重定向的感觉） | 无                                                           |
| echo    | echo [选项] [输出内容]                             | 输出内容到控制台                                             | 无                                                           |
| head    | head [选项] 文件                                   | 显示文件开头部分内容（默认前十行）                           | -n 数字：显示前数字行                                        |
| tail    | tail [选项] 文件                                   | 显示文件结尾部分内容（默认后十行）                           | -n 数字：显示后数字行<br />-f 实时追踪（在别的地方改了能看到） |
| ln      | ln -s [链接的文件或目录] [软链接名]                | 创建软链接（符号链接），相当于创建快捷方式（必须要绝对路径）<br />删除直接用rm -rf 软链接（**千万不要**带斜杠，要不然会把里面的东西都删掉） | 无                                                           |
| history | history [数字]                                     | 查看已经执行过的历史命令，加数字显示最新的指令，另：!编号表示执行该指令 | 无                                                           |

附：（看的时候按h可以看到所有快捷键）
空格键：下翻一页
enter键：下翻一行
q：离开
ctrl+F ：向下滚动一屏
ctrl+B ：返回上一屏
= ：输出当前行号
:f ：输出文件名和当前行行号

注：
按上下可以切换到之前的指令
环境变量：$PATH

#### 3.时间日期类

- date：全部显示
  选择显示：`date +%Y/%m/%d-%H:%M:%S`（如果中间有空格，加上”“）
  设置日期：`date -s “2022-1-19 17:07:34”`
  同步时间可以上网搜
- cal：当前日历情况
  显示一年：`cal 2022`

#### 4.搜索查找类

| 指令       | 语法                            | 作用                                           | 常用参数                                                     |
| ---------- | ------------------------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| find       | find [范围] [选项]              | 将遍历各级目录，将满足条件的目录显示出来       | -name 按名字查找<br />-user 按文件拥有者查找<br />-size [+20M] (+代表大于，-代表小于，不加代表等于) |
| locate     | locate 搜索文件                 | 首先用updatedb创建locate数据库，可快速查找     | 无                                                           |
| grep       | grep [选项] 查找内容 源文件     | 过滤查找                                       | -n 显示匹配行及行号<br />-i 忽略大小写                       |
| \|(管道符) | 案例: cat hello.txt \| grep yes | 将前一个命令的处理结果输出传递到后面的指令处理 | 无                                                           |

#### 5.压缩和解压缩

- `gzip`和`gunzip`（用的不多，压缩后原文件没有了）
  `gzip 文件` （将文件压缩成*.gz)
  `gunzip 文件.gz `（解压）

- `zip`和`unzip`（压缩文件或目录，原文件依旧存在）
  `zip [选项] XXX.zip` 要压缩的文件或目录（压缩到本文件夹）
  `unzip [选项] XXX.zip`
  zip常用：-r 压缩目录时用
  unzip常用：-d 指定解压后文件存放的目录

- `tar` （打包成 .tar.gz文件）
  `tar [选项] XXX.tar.gz` 要打包的文件

  | 选项 | 功能               |
  | ---- | ------------------ |
  | -c   | 产生 .tar打包文件  |
  | -v   | 显示详细信息       |
  | -f   | 指定压缩后的文件名 |
  | -z   | 打包同时压缩       |
  | -x   | 解包 .tar文件      |

  压缩套装：`tar -zcvf 目标文件名 文件1 文件2 ...`
  例如：`tar -zcvf a.tar.gz a1.txt a2.txt`
  解压缩套装：`tar -zxvf 要解压的文件 [-C 可选解压目录]` （前提：该目录原本就存在）

### 七、组管理和权限管理

#### 1.组管理

- 文件的所有者：一般来说即文件的创建者
- 查看文件的所有者	`ls -l` 或 `ll`就可以看到（也可以看到文件所在组）
- 修改文件所有者	`chown 所有者 文件名` （组不变）
- 修改文件所在组	`chgrp 所有者 文件名`
- 改变用户所在组	`usermod -g 组名 用户名`
- 改变用户登录的初始目录 `usermod -d 目录 用户名`

#### 2.权限管理

![](.\image\权限.png)

- 第0个： 文件的类型：- 普通文件	d 目录	l 软链接	c 字符设备【键盘，鼠标】	b 块文件，硬盘
  第1~3个：文件所有者对于这个文件的权限（rwx，有则显示，无则为 -）
  第4~6个：文件所在组的用户的权限（rwx，有则显示，无则为 -）
  第7~9个：文件其他组的用户的权限，同上
  后面那个数字：如果是文件，表示硬链接的数，如果是目录，表示目录的子目录数
  在往后两个：文件所有者和文件所在组
  文件大小
  文件最后的修改日期
  文件名
  
- rwx作用到文件
  r：可以读取，查看
  w：代表可写，可以修改，但是不代表可以删除，删除需要对所在目录具有可写的权限
  x：可以执行
  
- rwx作用到目录
  r：可以读取，可以用ls查看目录内容
  w：可以修改，包括目录内添加，删除，重命名
  x：可以执行，可以进入该目录

- rwx 的数字表示
  r：4  w：2  x：1
  7代表rwx，类似二进制表示，用数字即可代表这三个权限

- 修改权限：`chmod`（修改文件或目录的权限）（注：只有文件的所有者和root能修改权限）

  第一种方式：+（增加权限）、-（减少权限）、=（赋予）
  u：所有者 g：所在组 o：其他组 a：所有（即u+g+o）
  例 `chmod u=rwx,g=r,o=r 文件名`

  第二种方式：通过数字修改权限
  	`chmod u=rwx,g=r,o=r 文件名`   相当于 `chmod 744` 文件名

- 修改文件所有者：`chown`
  修改目录的所有者时，可以加 -R ，将文件内所有的文件全部chown
  `chgrp 加 -R 同上`

### 八、任务调度

任务调度指系统在某个时间执行的特定的命令或程序（定时调用）

- `crontab [选项]`
  -e 编辑crontab定时任务
  -l 查询crontab任务
  -r 删除当前用户所有的crontab任务
- service crontab restart 重启任务调度 

`*/1 * * * * ls -l /etc >> /tmp/to.txt`
命令解释：分钟 小时 天 月 星期 （每隔1分钟执行一次）

- 特殊符号说明
  | 特殊符号 | 说明 |
  | --- | --- |
  | * | 通配符，代表任意时间 |
  | ，| 不连续时间，如3,4,5 |
  | - | 连续时间，如1-4 |
  | */n | 每隔多久 |
- 编写shell脚本，并定时执行
  1. 编写一个文件 `/home/mytask.sh`
  内容：`date >> /tmp/mydate.txt`
  2. 给文件执行权限
  3. `crontab -e`
  4. `*/1 * * * * /home/mytask.sh`
  5. 成功

### 九、linux磁盘
#### 1.磁盘分区与挂载
- 查看系统的分区和挂载情况 `lsblk -f` （记忆：老师不离开）可以单独使用
- 硬盘分区情况 分区类型 标识分区的字符串 挂载点
- 实操：增加一块新硬盘挂载到`/home/mydisk`
  1. 设置虚拟机，增加新硬盘
  2. 分区：`fdisk /dev/sdb` （可以通过lsblk命令得到磁盘的名字） ->
    n -> p -> 1 -> 默认 -> 默认 -> w
  3. 格式化：`mkfs -t ext4(或xfs等) /dev/sdb1` （powershell右键粘贴）
  4. 挂载：`mount /dev/sdb1 /home/newdisk`
  5. 设置永久挂载：`vim /etc/fstab`
      在里面添加：`/dev/sdb1               /home/newdisk           ext4    defaults        0 0`
      生效命令：`mount -a`
  6. 取消挂载：`umount /dev/newdisk`
#### 2.磁盘使用情况查询
- 查询系统整体磁盘使用情况：`df -lh`
- 查询指定目录的磁盘占用情况：`du -h 目录`
  例：`du -ach --max--depth=1 /opt`
- `ls -l | grep "^-" | wc -l` 统计本目录下文件的个数
- `ls -lR | grep "^d" | wc -l` 递归统计本目录下文件夹的个数（注：此处R是递归，r是reverse）
- 树状显示：`tree`

### 十、网络配置
#### 1.原理和说明
- windows通过虚拟网卡与虚拟机联通，在关闭防火墙的基础上，可以互相ping通
- windows和linux通过VMnet8联通（虚拟网卡），windows和外界通过真实网卡，再通过网关联通
#### 2.自动获取ip
避免每次登录后手动连接
图形界面中，wired后有个齿轮，选中自动连接，apply即可
#### 3.修改配置文件指定ip
修改文件：`vim /etc/sysconfig/network-scripts/ifcfg-ens33`
```
IPADDR=192.168.2.130
GATEWAY=192.168.2.2
DNS1=192.168.2.2
```
`BOOTPROTO=dhcp` 改成 `BOOTPROTO=static`
确保 `ONBOOT=yes`

最后`reboot`生效

### 十一、进程管理
#### 1.介绍和查询
- 显示系统执行的全部进程：`ps -aux`
- 指定显示进程，例：`ps -aux | grep sshd`
- STAT行：S休眠 N优先级低 R正在运行 D短期等待 Z僵死进程 T被跟踪或被停止
- 查看副进程：`ps -ef` （PPID显示行
#### 2.终止进程
`gedit` 编辑text
- 找到目标进程获取进程id，然后 `kill id号`
- `killall +名字`
- 强制删除：加参数 `-9`
- 进程的树状显示：`pstree`
  参数：-p 显示pid    -u 显示用户
#### 3.服务管理
每个服务有一个端口，外界通过端口链接到linux中的服务上
- `systemctl 【start，stop等】 服务名`
- 检查Linux某个端口是否在监听，并且可以访问：在windows输入`telnet 192.168.2.130 22`（需要开启windows的telnet功能）
- `systemctl list-unit-files` 查看所有服务（`--type=service` 可选）
- 永久关闭服务例：`systemctl disable sshd.service`（开启：enable）
- 如果没生效，重启即可
#### 4.监控服务
- top 实时监控服务，在运行top的时候可以输入参数：
  u 回车 可以指定用户
  k 回车 可以杀死进程
  `top -d 10` 回车 指定更新时间（默认3秒）
  P(M,N) 回车 按顺序显示(CPU,内存,PID)
  q 退出
- `netstat -anp` 查看系统所有的网络服务

### 十二、RPM包和YUM
#### 1.RPM包基本信息
- RPM包：相当于安装程序
- 查询已安装的RPM包：`rpm -qa | grep xxx`
- rpm包名基本格式：软件名 版本 适用系统
- | 指令 | 功能 |
  | ---  | --- |
  | rpm -qa | 显示所有的rpm软件包 |
  | rpm -q 软件包名 | 查看指定软件包是否安装 |
  | rpm -qi 软件包名 | 查看指定软件包的信息 |
  | rpm -ql 软件包名 | 查询指定软件包中的文件（可以知道rpm包安装到哪里去了） |
  | rpm -qf 文件全路径名 | 查询文件所属的软件包|
#### 2.RPM包的卸载和安装
- 删除：`rpm -e 软件包的名字`
  强制删除：加 `--nodeps` （尽量不要用）
- 安装：`rpm -ivh RPM包全路径名称`
- rpm包在 `/run/media/用户名/Appstream/Packages/` 里面
#### 3.yum
- 从网站下载rpm包并安装，会自动安装依赖
- 查看安装软件：`yum list | grep XXX`
- 安装软件：`yum install XXX`
- 前提：可以联网
- 如果yum失败：
  ```
  mkdir /tmp/yum
  cd /etc/yum.repos.d
  mv *.repo /tmp/yum
  wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-vault-8.5.2111.repo
  yum clean all
  yum makecache
  ```

### 十三、shell编程
#### 1.格式要求
- 脚本以 `#!/bin/bash` 开头
- 脚本需要有可执行权限
- 执行方式：
  - 在有执行权限时直接输入绝对路径
  - 在有执行权限时直接输入相对路径
  - 不用执行权限：`sh 路径`
#### 2.变量
分为**系统变量**和**用户自定义的变量**
- #可以注释
- 系统变量：```$USER $HOME $PATH``` 等等
- set命令显示所有系统变量
- 自定义普通变量：
  - 如：```A=100```（注：等号两侧不可以有空格,且变量一般大写）
  - 引用时用：```$A```
  - unset可以取消变量
- 自定义静态变量
  - 如 ```readonly A=99```
  - 不可以```unset```
- 可以把变量提升为全局环境变量，供其他shell程序使用
- 运行里面的命令，把结果返回给变量
  - 第一种：```A=`ls -l` ```(反引号法)
  - 第二种：```A=$(date)```($()法)
#### 3.设置环境变量
- 基本语法
  - 进入```/etc/profile```
  - ```
    变量名=变量值
    export 变量名

    例：
    TOMCAT_HOME=/opt/tomcat
    export TOMCAT_HOME
    ```
  - `source /etc/profile`
- 多行注释：`:<<!     !`
- `echo $变量名`可输出环境变量
#### 4.位置参数变量
用于获取执行脚本时提供的参数
```bash
echo "$0 $1 $2" # $n获取第n个参数,10以上要加{}，如 ${10}
echo "$*" # $*获取每个参数并当作整体
echo "$@" # $@获取每个参数并分别对待
echo "$#" # $#获取参数的个数
```
运行`./myshell.sh 100 200`输出为
```
./myshell.sh 100 200
100 200
100 200
2
```
#### 5.预定义变量
```bash
$$ #获取当前进程的进程号
$! #后台运行的第一个进程的进程号
$? #返回最后一次执行的命令的执行状态，0代表成功了，非零代表没有成功
```
- 在后台运行`命令名 &`
#### 6.运算符
```shell
$(()) #方法一
$[] #方法二，推荐
`expr `#方法三，运算符两侧要有空格，一次一个表达式，乘号为\*（转义）
```
#### 7.条件判断
- 判断语句：`[ condition ]`(注：条件前后要有空格)
```shell
#判断条件
if [ "ok" = "ok" ] # =用于字符串比较,根据高亮自行判断空格
if [ 23 -gt 22 ] # -gt相当于>
if [ -e /root/shell/aaa.txt ] #判断文件是否存在

# = 字符串比较
# -lt 小于
# -le 小于等于
# -eq 等于
# -gt 大于
# -ge 大于等于
# -ne 不等于

# -r 读的权限
# -w 写的权限
# -x 执行的权限
# -f 文件存在且是常规文件
# -e 文件存在
# -d 文件存在且是目录
```
#### 8.流程控制
- if判断
```shell
if [ 条件判断式 ]
  then
    程序
elif [ 条件判断式 ]
  then
    程序
else
  程序
fi
```
- case语句
```shell
case 表达式 in
值1)
程序
;;
值2)
程序
;;
*)
程序
;;
esac
```
- for循环
```shell
#基本语法1
for i in "$*"
do
  echo "the number is $i"
done

for i in "$@"
do
echo "the number is $i"
done

#对于$*，不加引号，输出和下面一样，加引号，输出是一个整体

#基本语法2
for((i=1;i<=100;i++))
do
  SUM=$[$SUM+$i]
done
```
- while循环
```shell
SUM=0
i=0
while [ $i -le $1]
do
  SUM=$[$SUM+$i]
  i=$[$i+1]
done
```
#### 9.读取控制台输入read
```shell
read -t 10 -p "请输入：" NUM
# -p后跟提示信息
# -t后跟限制读取的时间(s)
```
#### 10.函数
  -系统函数：
  返回完整路径最后/部分，常用于获取文件名：`basename /home/aaa/test.txt`（末尾空格加上后缀可以去掉后缀
  跟上面相反，得到路径部分：`dirname /home/aaa/test.txt`
  - 自定义函数
  ```shell
  #定义
  function getsum(){
    SUM=$[$n1+$n2]
    echo "和是：$SUM"
  }
  n1=$1
  n2=$2
  #调用
  getsum $n1 $n2
  ```
#### 11.项目实战
- 需求：
1.每天凌晨2.10备份数据库Try到/data/backup/db
2.备份开始和结束能够给出相应的提示信息
3.备份后的文件以备份时间为文件名，，并打包成.tar.gz的形式，如：2022-03012——101902.tar.gz
4.在备份的同时，检查是否有10天前备份的数据库文件，如果有将其删除
- 实现
1.将.sh放在/usr/bin下
2.代码
```shell
#!/bin/bash

#完成数据库的定时备份
#备份的路径
BACKUP=/data/backup/db
#当前时间
DATETIME=$(date +%Y_%m_%d_%H%M%S)
#echo $DATETIME

echo "========开始备份========"
echo "========备份的路径是 $BACKUP/$DATETIME.tar.gz"

#主机
HOST=localhost
#用户名
DB_USER=root
#密码
DB_PWD=root
#备份数据库名
DATABASE=Try
#创建备份路径
#如果路径有了，就使用，否则创建
[ ! -d "$BACKUP/$DATETIME" ] && mkdir -p "$BACKUP/$DATETIME"
#执行mysql的备份数据库的指令
cd $BACKUP
mysqldump -u${DB_USER} -p${DB_PWD} --host=$HOST $DATABASE | tar -zcvf $DATETIME.tar.gz $DATETIME
rm -rf $BACKUP/$DATETIME

#删除10天前的
find $BACKUP -mtime +10 -name "*.tar.gz" -exec rm -rf {} \;
echo "=========备份成功==========="
```
### 十四、一点ubuntu
#### 1.apt软件管理
在`/etc/apt/source.list`指定了官方的软件仓库【美国】
- 一些指令
```bash
sudo apt-get update 更新源
sudo apt-get install package 安装包
sudo apt-get remove package 删除包
sudo apt-cache show pachage 获取包的信息
sudo apt-get source pachage 下载包的源代码
```
- 可修改镜像源
https://mirrors.tuna.tsinghua.edu.cn/ 清华的镜像源（好像不好用，建议阿里源）
在镜像源中找到ubuntu
先备份再清空粘贴
最后更新源（命令如上）
#### 2.ssh远程登录
sudo apt-get install openssh-server
service sshd start