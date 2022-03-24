## 查看系统主机名
* uname -n
* hostname
* hostnamectl

## 打包解压
```
`打包war包  jar -cvf war包名字  将要打包的资源 *.*/`
`无需解压，放到tomcat服务器webapps中即可`
`解压命令 tar -xvf xx.tar`
```

## 重启服务 进入/etc/init.d 输入 service apache2 restart

## 查看端口 lsof -i:80
## kill -x (x表示PID，进程的id，杀死进程)
* 如果提示没用这命令，那么使用kill -9 pid

## 删除文件/目录 rm -r  xxx 即使该文件/文件夹下面还有文件，还是会递归删除
## 修改文件名 mv oldname newname

## Linux权限
1. 以chmod 777为例，表示`文件所有者，群组用户，其他用户对该文件的权限皆为可读，可运行，可写`
2. `chmod命令是三位数，第一位表示文件所有者，第二位表示群组用户，第三位表示其他用户`
3. `chmod命令的数字为7表示可读可运行可写，6表示可读可写，5表示可读可运行，4表示可读`
* `-r可读4，-w可写2，-x可执行1`
* `chmod 765 => (4+2+1)(4+2)(4+1) => rwxrw-r-x`

## find查找文件或目录
```text
-type 按类型进行查找，d查找目录，f查找文件

find . –type d –name [document]

find . type f –name [filename]
```
1. 查找文件
`find / -name nginx.conf  表示以/为根目录查找名称为nginx.conf的文件`
2. 查找目录
`find ~/Documents/blog/blog -type d -name vue 表示以~/Documents/blog/blog为根目录查找文件夹名称为vue的文件夹`

## grep在文件中查找字符串

## .tar.gz是tar的压缩文件
## .gz是另一种压缩文件，不一样的！

## shell中，将command1的输出作为command2的输入 command2 | command1

## 查看文件
1. cat全部输出
2. more分页查看
3. less使用光标上下移动一行

## 显示当前工作目录命令pwd (print working directory打印当前工作目录)

## 文件exer1的访问权限为rw-r--r--，现要增加所有用户的执行权限和同组用户的写权限
1. `r表示读权限 w表示写权限 x表示运行权限`
2. `a表示所有人 u表示拥有者 g表示所属群组`
3. `所以可以用chmod a+x,g+w exer1`

## linux恢复
1. 不小心commit了，如何恢复到上一状态？`最好用git reset --soft HEAD^`
`如果用git reset --hard HEAD^具有破坏性，导致难以恢复`
2. 回退到上几个commit状态用`git reset --soft HEAD~x(x在这里就是一个数量)`

## linux系统的cron服务用于系统日常资源的调度
## linux系统中网络管理员对WWW服务器进行访问，控制存取和运行是通过httpd.conf文件体现的

## netstart用于打印网络的状态
## top用于获取本机的cpu使用率
## uptime用于打印系统运行时间和系统平均负f载
## export用于设置环境变量
## env用于查询环境变量
## echo用于显示一段文字
## cat用于查看文件内容，创建文件，文件合并，追加文件等

## tail -100f error.log 查看error.log文件的最后100行

## 链接
* 硬链接就是同`一个文件使用了多个别名`
* 软链接就是:文件用户数据块中存放的是`对另一文件的文件路径指向`

## 常见目录
* /etc: 配置目录
* /home: 普通用户的主目录
* /boot: 启动目录
* /lib: 系统库
* `/tmp: 临时目录`
* /sys: 硬件设备的驱动程序信息
* /var: 变量


## open .
* `在命令行界面输入 open . 在finder打开当前文件目录`

## ll
ll查看文件权限

## dd
添加文件，但是还有很多用处

## ps
* `process status显示进程的状态`

## grep
* grep命令用于查找文件里符合条件的字符串。
