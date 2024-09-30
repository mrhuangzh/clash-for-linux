
# ubuntu 安装 clash

## 目的
通过 `clash` ，使得 `ubuntu` 服务器能够访问 `目标服务器`，实现加速或 ·github· 稳定访问等。
## 步骤一：下载 clash
下载 ：[https://github.com/mrhuangzh/clash-for-linux](https://github.com/mrhuangzh/clash-for-linux)
点击 `code` -> `Download zip`，下载 `zip` 文件
![image](https://github.com/user-attachments/assets/be746d1a-e674-4d88-8c17-b79a4c4008b6)

## 步骤二：上传服务器
1、将下载的zip文件上传至服务器
```cmd
# ll
total 12572
drwxr-xr-x 2 root root     4096 Sep 26 10:02 ./
drwxr-xr-x 3 root root     4096 Sep 26 10:01 ../
-rw-r--r-- 1 root root 12862023 Sep 26 10:02 clash-for-linux-master.zip
```
2、解压zip文件
```cmd
# unzip clash-for-linux-master.zip
# ll
total 12576
drwxr-xr-x 3 root root     4096 Sep 26 10:05 ./
drwxr-xr-x 3 root root     4096 Sep 26 10:01 ../
drwxr-xr-x 7 root root     4096 Apr  9  2023 clash-for-linux-master/
-rw-r--r-- 1 root root 12862023 Sep 26 10:02 clash-for-linux-master.zip
```
## 步骤三：配置 clash
### 1、进入解压后的 `clash-for-linux-master` 目录。
```cmd
# cd clash-for-linux-master
```
### 2、编辑`.env`文件，修改变量`CLASH_URL`的值为订阅地址。
`.env` 文件中的变量 `CLASH_SECRET` 为自定义 Clash Secret，值为空时，脚本将自动生成随机字符串，可以空着。
```cmd
# vim .env
```
![image](https://github.com/user-attachments/assets/359d2467-e0cc-451e-850c-6ac182685715)

### 3、启动程序
```cmd
# sudo bash start.sh

正在检测订阅地址...
Clash订阅地址可访问！                                      [  OK  ]

正在下载Clash配置文件...
配置文件config.yaml下载成功！                              [  OK  ]

正在启动Clash服务...
服务启动成功！                                             [  OK  ]

Clash Dashboard 访问地址：http://<ip>:9090/ui
Secret：eda0df0e6665ed2xxxxxxxxxxxxxxxxxxxxxxxxx

请执行以下命令加载环境变量: source /etc/profile.d/clash.sh

请执行以下命令开启系统代理: proxy_on

若要临时关闭系统代理，请执行: proxy_off
```
### 4、根据提示依次执行命令
```cmd
# source /etc/profile.d/clash.sh
# proxy_on
[√] 已开启代理
```
### 5、检查服务端口
```cmd
# netstat -tln | grep -E '9090|789.'
tcp6       0      0 :::9090                 :::*                    LISTEN
tcp6       0      0 :::7890                 :::*                    LISTEN
tcp6       0      0 :::7891                 :::*                    LISTEN
tcp6       0      0 :::7892                 :::*                    LISTEN
```
### 6、检查环境变量
```cmd
# env | grep -E 'http_proxy|https_proxy'
https_proxy=http://127.0.0.1:7890
http_proxy=http://127.0.0.1:7890
```
### 7、查看终端中是否可以访问目标服务器如搜索网站等
可正常访问：
```cmd
# curl https://www.google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>302 Moved</TITLE></HEAD><BODY>
<H1>302 Moved</H1>
The document has moved
<A HREF="https://www.google.com.hk/url?sa=p&amp;hl=zh-CN&amp;pref=hkredirect&amp;pval=yes&amp;q=https://www.google.com.hk/&amp;ust=1727318388553155&amp;usg=AOvVaw1GXpeDoaba7wTT2otAmfKM">here</A>.
</BODY></HTML>
```

### 8、新建终端检测是否可以访问
```cmd
# echo $http_proxy
新终端无系统代理环境变量
# curl https://www.google.com
新终端无法访问外网
```
### 9、其他终端无法访问目标服务器解决方法
#### 方法一：临时设置代理（仅对当前会话有效）
```cmd
# export http_proxy=http://127.0.0.1:7890
# export https_proxy=http://127.0.0.1:7890
# curl http://www.google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>302 Moved</TITLE></HEAD><BODY>
<H1>302 Moved</H1>
The document has moved
<A HREF="http://www.google.com.hk/url?sa=p&amp;hl=zh-CN&amp;pref=hkredirect&amp;pval=yes&amp;q=http://www.google.com.hk/&amp;ust=1727318635293778&amp;usg=AOvVaw1EcsWf7V7kk489rrJ_HlxI">here</A>.
</BODY></HTML>
```
#### 方法二：永久设置代理（对所有新会话有效）
在 ~/.bashrc 配置文件中添加系统代理，并刷新配置
```cmd
# vim ~/.bashrc
# source ~/.bashrc
```
![image](https://github.com/user-attachments/assets/bcd7cabe-5fd4-436f-9de9-e72e9642e546)

### 10、新建终端，测试连接，可正常访问
```cmd
# curl https://www.google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>302 Moved</TITLE></HEAD><BODY>
<H1>302 Moved</H1>
The document has moved
<A HREF="https://www.google.com.hk/url?sa=p&amp;hl=zh-CN&amp;pref=hkredirect&amp;pval=yes&amp;q=https://www.google.com.hk/&amp;ust=1727318828490860&amp;usg=AOvVaw04dUkcVg0B2Kczy49BvvLs">here</A>.
</BODY></HTML>
```
### 11、使用 sudo curl https://www.google.com 时代理失效
默认情况下，sudo 会清除环境变量。
#### 解决方法一：
如果需要在使用 sudo 时保留代理设置，可以使用 -E 选项：
```cmd
# sudo -E http_proxy=http://127.0.0.1:7890 https_proxy=http://127.0.0.1:7890 curl https://www.google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>302 Moved</TITLE></HEAD><BODY>
<H1>302 Moved</H1>
The document has moved
<A HREF="https://www.google.com.hk/url?sa=p&amp;hl=zh-CN&amp;pref=hkredirect&amp;pval=yes&amp;q=https://www.google.com.hk/&amp;ust=1727318917423991&amp;usg=AOvVaw0Hd7h9cjlHqntNCMJ-skTe">here</A>.
</BODY></HTML>
```
#### 解决方法二：
更新配置文件 /etc/sudoers ，加入 Defaults env_keep += "http_proxy https_proxy" 
```cmd
# sudo chmod +w /etc/sudoers
# sudo vim /etc/sudoers
```
![image](https://github.com/user-attachments/assets/425e9a26-d4d0-47d4-96e7-32d583ff7b1e)

此时 sudo 已可直接使用系统代理
```cmd
# sudo curl https://www.google.com
<HTML><HEAD><meta http-equiv="content-type" content="text/html;charset=utf-8">
<TITLE>302 Moved</TITLE></HEAD><BODY>
<H1>302 Moved</H1>
The document has moved
<A HREF="https://www.google.com.hk/url?sa=p&amp;hl=zh-CN&amp;pref=hkredirect&amp;pval=yes&amp;q=https://www.google.com.hk/&amp;ust=1727319095030180&amp;usg=AOvVaw32VzugSSHhHK9YnC4Yt9aR">here</A>.
</BODY></HTML>
```
