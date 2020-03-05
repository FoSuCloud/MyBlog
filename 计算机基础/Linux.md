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
## 查看端口 Isof -i:80
## 删除文件/目录 rm -r  xxx 即使该文件/文件夹下面还有文件，还是会递归删除
## 修改文件名 mv oldname newname

## Linux权限
1. 以chmod 777为例，表示`文件所有者，群组用户，其他用户对该文件的权限皆为可读，可运行，可写`
2. `chmod命令是三位数，第一位表示文件所有者，第二位表示群组用户，第三位表示其他用户`
3. `chmod命令的数字为7表示可读可运行可写，6表示可读可写，5表示可读可运行，4表示可读`

## find查找文件或目录
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
