# 第七週筆記
## Centos7編譯核心
* 編譯核心
    * uname -r 確認使用的版本
    * 到[kernel](https://www.kernel.org/)網站，選擇4.19.238的版本，右鍵複製tarball的鏈接 
    * 虛擬機wget+網址，虛擬機必須連網
    * 解壓縮tar xvjf linux-4.19.237.tar.xz
    * cd linux-4.19.237.tar.xz
    * cp -v /boot/config-3.10.0-1127.19.1.el7.x86_64 .config
    * make menuconfig會跳出圖形化介面，按save和exit
    * 依序執行make bzImage、make modules、make、makem odules_install、make install，注意此過程執行時間較長。如果發生錯誤執行make clean後再執行發生錯誤的步驟
    * grub2-mkconfig -o /boot/grub2/grub.cfg
    * 完成後重啟系統

```
uname -r
yum update
yum install -y ncurses-devel make gcc bc bison flex elfutils-libelf-devel openssl-devel grub2
wget https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.19.238.tar.xz
tar xvjf linux-4.19.237.tar.xz
cd /boot
ll -h
cd
cd linux-4.19.237.tar.xz
cp -v /boot/config-3.10.0-1127.19.1.el7.x86_64 .config
make menuconfig
```
## jumpserver
* 安裝Docker Compose
```
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose
chmod +x /usr/local/bin/docker-compose
systemctl status docker
docker-compose --version
```
* jumpserver
    * 如果出現error 用docker-compose down
        * docker ps -a 有些需要手動刪除
    * 如果部屬失敗，用docker -ps查看，把剛建立的容器刪掉
        * docker rm -f$(docker ps -a-q)
```
git clone --depth=1 https://github.com/wojiushixiaobai/Dockerfile.git
cd Dockerfile/
cp config_example.conf .env
gedit .env
內容
# 版本号可以自己根据项目的版本修改
Version=v2.20.3

# 构建参数, 支持 amd64/arm64
TARGETARCH=amd64

# Compose
COMPOSE_PROJECT_NAME=jms
COMPOSE_HTTP_TIMEOUT=3600
DOCKER_CLIENT_TIMEOUT=3600
DOCKER_SUBNET=172.20.0.0/16

# 持久化存储
VOLUME_DIR=/opt/jumpserver

# MySQL
DB_HOST=mysql
DB_PORT=3306
DB_USER=root
DB_PASSWORD=nu4x599Wq7u0Bn8EABh3J91G
DB_NAME=jumpserver

# Redis
REDIS_HOST=redis
REDIS_PORT=6379
REDIS_PASSWORD=8URXPL2x3HZMi7xoGTdk3Upj

# Core
SECRET_KEY=B3f2w8P2PfxIAS7s4URrD9YmSbtqX4vXdPUL217kL9XPUOWrmy
BOOTSTRAP_TOKEN=7Q11Vz6R2J6BLAdO
DEBUG=FALSE
LOG_LEVEL=ERROR
以上為內容
docker-compose -f docker-compose-redis.yml -f docker-compose-mariadb.yml -f docker-compose.yml up
```
* 輸入iP就會到Jumpserver 預設帳號密:admin 
* users下的users->Create
* Assets
    * System Users ->create
        * ssh
        * Common user
                * name: user
                * username: user
            Admin user
            * name: root
            * username: user
    * Assets ->create
        * Hostname: centos7-1
        * IP: 192.168.56.101
        * Platform: Linux 
        * protocols ssh 22
        * Admin user:root
        * Nodes /Default


* 資產權限=> Permissions
    * Asset Permission->create
        * name: forTomCentos7-1
        * Asset centos7-1(192.168.56.101)
        * Nodes: /Default
        * system user :user(user)
        * Action:all
* 有兩種連線方式
    * 網頁版
         * 登出 登入tom帳號
         * view->workbench
         * web terminal
    * putty
        * ip 埠號2222 (jumpserver)
        * 帳tom密12456
        * s ->1
* 拒絕其他使用者危險動作
    * Users
        * Command Filters
            * Name:ForTom
            * user:tom
            * Asset :centos7-1(ip)
        * Command Filters rules
            * type :command
            * content: rm -f
            * Action :Deny
    

## 參考網址
* [CentOS 7 編譯核心](https://www.ltsplus.com/linux/rhel-centos-7-compile-kernel)
* [jumpserver](https://github.com/jumpserver/Dockerfile)