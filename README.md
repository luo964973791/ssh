### 第一种SSH反向代理不能访问谷歌的方法
```javascript
服务器A: 172.27.0.3可以联网的服务器执行：
yum install squid -y
acl allsrc src 0.0.0.0/0
http_access allow allsrc
http_access allow localhost


systemctl restart squid

ssh -R 3128:172.27.0.3:3128 root@172.27.0.5 -N
使用完毕关闭ssh反向代理隧道命令：exit

服务器B：172.27.0.5不能联网的服务器上面执行使用代理,访问172.27.0.5:3128端口会转发到172.27.0.3:3128端口上面，方法二可以访问谷歌的方法有区别,注意看仔细IP怎么用:
cat /etc/ssh/sshd_config | grep -E 'AllowTcpForwarding|GatewayPorts'
AllowTcpForwarding yes
GatewayPorts yes


systemctl restart sshd
export http_proxy=172.27.0.5:36000
export https_proxy=172.27.0.5:36000
export ftp_proxy=172.27.0.5:36000
```
### 第二种SSH反向代理可以访问谷歌的方法，有Clash for windows就不用安装tinyproxy这个包了.
```javascript
服务器A linux可以联网：  172.27.0.3
服务器B Linux不可以联网: 172.27.0.5
服务器C windows：172.27.0.6

服务器A: 上面执行如下命令：
sed -i 's/#AllowTcpForwarding no/AllowTcpForwarding yes/' /etc/ssh/sshd_config && sed -i 's/GatewayPorts no/GatewayPorts yes/' /etc/ssh/sshd_config
/usr/bin/ssh -R 36000:172.27.0.6:36000 root@172.27.0.3 -N

服务器B：172.27.0.5不能联网的服务器上面执行使用代理,访问172.27.0.3:36000端口会转发到172.27.0.6:36000 windows服务器的端口上面,和上面第一种不能访问谷歌的方式IP有区别,仔细看:
export http_proxy=172.27.0.3:36000
export https_proxy=172.27.0.3:36000
export ftp_proxy=172.27.0.3:36000
```
![image](https://github.com/user-attachments/assets/ed858a5d-324c-4fd9-a334-a346e566b0f9)
