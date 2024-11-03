> 1、ps -ef|grep 进程名称                 #查看进程

> 2、 systemctl status firewalld            # 暂时关闭防火墙

    systemctl stop firewalld.service      # 禁用防火墙自启动

    systemctl disable firewalld.service  #关闭开机启动

    sudo systemctl status firewalld      # 查看防火墙活动状态，如果服务是disabled或inactive，那么firewalld就关闭的

> 3、查看文件 vi ；进入到文件后按字母 i 即可编辑，编辑完成后按Esc，按住shift加上：号，然后输入”wq”表示保存退出，”q！”表示不保存退出；

> 4、杀死某一个进程命令        kill -9  PID

> 5、更改文文件和目录的所有权  chown -R shenlan:shenlan mynotes   #将mynotes文件的所有权将root用户变成shenlan用户

> 6、查看启动的端口         netstat -nltp

> 7、载入sysctl配置文件     sysctl -p

> 7、显示文件系统信息       lsblk -f 

> 8、通过cmd打开记事本 输入 notepad

> 9、移动文件到某一个位置   mv text.zip   /opt/module/

> 10、tar.gz包文件解压命令  tar -zxvf   test.tar.gz
         zip文件解压       unzip -n    text.zip

> 11、df -h   查看当前目录磁盘的使用情况


> 11 后台启动jarbao的命令  nohup java -jar > log.log 2>&1 &

> 12 删除文件   rm -rf 文件名

> 13 查看文件的权限   ll

> 14 查看剩余空间  free -h

> 15 查看活动连接的端口 netstat -ano|findstr 11000

> 16 查看ip命名  ip addr

> 17 使用命名查看接口是否可以调用  curl -X POST "http://192.168.43.203:8080/token/sign?appId=8SklH9Jq&appSecret=VpYmbkWUjndra0dMv3pe65Jzof742VpS"

> 18 显示所有的文件地址及其运行程序的pid：  ps aux

> 19 给文件下面的所有赋予 -rwxr-xr-x 权限命令: chmod -R 755 文件夹路径

> 20 动态查看日志   tail -F log.log

> 21、杀死进程：  pkill -9 test（项目名称）

> 22、查看运行的Java进程    ps -ef|grep java；查看java进程 ps -aux|grep java ;（-ef会列出所有进程的完整信息，包括进程的 UID 和 PID）根据项目名查看进程： ps -aux|grep 项目名   

> 23、全盘扫描查找文件命令：sudo find / -name iSpaceExample.jar

> 23、查看历史java的命令：history | grep java

> 24、使用ssh连接服务器：ssh -p 端口号 linux服务器用户名@linux服务器ip地址

 
