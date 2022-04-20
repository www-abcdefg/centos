# 第三週筆記
## Linux network namespace 原理與實踐
* ip netns add 創造一個空間
* ip netns ls查看有無創建
* ip netns del net1 刪除空間
* ip netns exec net1 ifconfig -a 看所有
* ip netns exec net1 ifconfig lo up 啟動
* veth pair對稱的虛擬網路卡

![pic]()

* ip link add type veth 建立網路卡
* ifconfig -a 剛該使創造網路卡預設是未開啟的
* ip netns exec net0 ip link set dev veth0 name eth0 把veth0改成eth0
* ip netns exec net0 ip addr add 10.0.1.1/24 brd + dev etho 配IP
```
ip netns add net0
ip netns ls
ip netns add net1
ip netns exec net1 ifconfig
ip netns exec net1 ip addr
ip netns exec net1 ifconfig lo up
ip link add type veth
ifconfig -a
ip link set dev veth0 netns net0
ip link set dev veth1 netns net1
ip netns exec net0 ifconfig -a
ip netns exec net1 ifconfig -a
ip netns exec net0 ip link set dev veth0 name eth0
ip netns exec net1 ip link set dev veth1 name eth0
ip netns exec net0 ip addr add 10.0.1.1/24 brd + dev etho
ip netns exec net0 ifconfig eth0 up
ip netns exec net1 ip addr add 10.0.1.2/24 brd + dev etho
ip netns exec net1 ifconfig eth0 up
ip netns exec net0 ping 10.0.1.2
開個新視窗
su
cd
ip netns exec net1 echo "hi" > hi.htm
ip netns exec net1 python -m SimpleHTTPServer 80
回原本視窗
ip netns exec net0 curl http://10.0.1.2/hi.htm
```
* ip netns exec net0 bash 開出一個殼
* su -user 切換使用者
* firefox 開啟火狐狸輸入10.0.1.2/hi.htm
```
ip netns exec net0 bash
su - user
firefox
```
* 多个不同 namespace 之间的通信
* brctl addbr br0創造橋接器

![pic2]()

```
ip netns add net0
ip netns add net1
ip netns add net2
ip link add type veth
ip link add type veth
ip link add type veth
ip link set dev veth1 netns net0
ip link set dev veth3 netns net1
ip link set dev veth5 netns net2
ip netns exec net0 ip link set dev veth1 name eth0
ip netns exec net0 ip addr add 10.0.1.1/24 brd + dev etho
ip netns exec net0 ifconfig eth0 up
ip netns exec net0 ifconfig
ip netns exec net1 ip link set dev veth3 name eth0
ip netns exec net1 ip addr add 10.0.1.2/24 brd + dev etho
ip netns exec net1 ifconfig eth0 up
ip netns exec net1 ifconfig
ip netns exec net2 ip link set dev veth5 name eth0
ip netns exec net2 ip addr add 10.0.1.3/24 brd + dev etho
ip netns exec net2 ifconfig eth0 up
ip netns exec net2 ifconfig
yum install bridge-utils
brctl addbr br0
brctl addif br0 veth0
brctl addif br0 veth2
brctl addif br0 veth4
brctl show br0
ifconfig br0 up
ifconfig veth0 up
ifconfig veth2 up
ifconfig veth4 up
ip netns exec net0 ping 10.0.1.2
ip addr add 10.0.1.254/24 brd + dev br0
```
## docker
* 映像檔 Image
    * Docker 映像檔是一個模板，用來重複產生容器實體
* 容器 Container
    * 容器是用映像檔建立出來的執行實例，它可以被啟動、開始、停止、刪除。每個容器都是相互隔離、保證安全的平台。
* 倉庫 Repository
    * 倉庫（Repository）是集中存放映像檔檔案的場所
* docker用了兩種技術去完成
    * 隔離技術
    * cgroups
* docker的家目錄在 /var/liv/docker/
* 下載docker
    * docker -v 測試是否安裝成功 會出現Docker version
```
yum install -y yum-utils
 sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo

sudo yum install docker-ce docker-ce-cli containerd.io

y

docker -v
```
* docker
    * docker ps可以看多少docker再跑 和docker id
    * 如果exit只是把狀態為run改為exit用docker ps -a可以看到
    * 第一種刪除用名稱 第二種用docker id(通常寫前三個字)
        * ex: docker rm e31 
```
docker images
docker run hello-world
docker images
docker run -it ubuntu echo "hi"
docker run -it ubuntu bash
docker run -it ubuntu:latest bash
docker ps
```
## 參考網址
* [详解云计算网络底层技术——Linux network namespace 原理与实践](https://segmentfault.com/a/1190000018391069?fbclid=IwAR3fZ4aXCqTjvZ-l8SsCd2UPuzuw6v8wRV_kB_n5yayenQn3zyQekOsDUao)
* [Docker 基礎教學與介紹 ](https://cwhu.medium.com/docker-tutorial-101-c3808b899ac6)