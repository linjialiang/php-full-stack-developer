# FTP 命令

ftp 命令用来设置文件系统相关功能。
ftp 服务器在网上较为常见，Linux ftp 命令的功能是用命令的方式来控制在本地机和远程机之间传送文件，这里详细介绍 Linux ftp 命令的一些经常使用的命令，相信掌握了这些使用 Linux 进行 ftp 操作将会非常容易。

## 语法

```sh
ftp [选项] [参数]
```

1. 选项

```sh
-d：详细显示指令执行过程，便于排错或分析程序执行的情况；
-i：关闭互动模式，不询问任何问题；
-g：关闭本地主机文件名称支持特殊字符的扩充特性；
-n：不使用自动登录；
-v：显示指令执行过程。
```

2. 参数

参数：是指定 ftp 服务器的 ip 地址+端口号

3. 案例：

```sh
# FTP 服务器ip： 192.168.10.250
# FTP服务器端口： 2121
$ ftp [-v] [192.168.10.250] [2121]
# 选项可以省略
# 端口如果为21，也可以省略
```

## 实例

```sh
FTP>ascii: 设定以ASCII方式传送文件(缺省值)
FTP>bell: 每完成一次文件传送,报警提示.
FTP>binary: 设定以二进制方式传送文件.
FTP>bye: 终止主机FTP进程,并退出FTP管理方式.
FTP>case: 当为ON时,用MGET命令拷贝的文件名到本地机器中,全部转换为小写字母.
FTP>cd: 同UNIX的CD命令.
FTP>cdup: 返回上一级目录.
FTP>chmod: 改变远端主机的文件权限.
FTP>close: 终止远端的FTP进程,返回到FTP命令状态, 所有的宏定义都被删除.
FTP>delete: 删除远端主机中的文件.
FTP>dir [remote-directory] [local-file] 列出当前远端主机目录中的文件.如果有本地文件,就将结果写至本地文件.
FTP>get [remote-file] [local-file] 从远端主机中传送至本地主机中.
FTP>help [command] 输出命令的解释.
FTP>lcd: 改变当前本地主机的工作目录,如果缺省,就转到当前用户的HOME目录.
FTP>ls [remote-directory] [local-file] 同DIR.
FTP>macdef: 定义宏命令.
FTP>mdelete [remote-files] 删除一批文件.
FTP>mget [remote-files] 从远端主机接收一批文件至本地主机.
FTP>mkdir directory-name 在远端主机中建立目录.
FTP>mput local-files 将本地主机中一批文件传送至远端主机.
FTP>open host [port] 重新建立一个新的连接.
FTP>prompt: 交互提示模式.
FTP>put local-file [remote-file] 将本地一个文件传送至远端主机中.
FTP>pwd: 列出当前远端主机目录.
FTP>quit: 同BYE.
FTP>recv remote-file [local-file] 同GET.
FTP>rename [from] [to] 改变远端主机中的文件名.
FTP>rmdir directory-name 删除远端主机中的目录.
FTP>send local-file [remote-file] 同PUT.
FTP>status: 显示当前FTP的状态.
FTP>system: 显示远端主机系统类型.
FTP>user user-name [password] [account] 重新以别的用户名登录远端主机.
FTP>? [command]: 同HELP. [command]指定需要帮助的命令名称。如果没有指定 command，ftp 将显示全部命令的列表。
FTP>! 从 ftp 子系统退出到外壳。
```
