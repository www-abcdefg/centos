## 第四周筆記
## image
* image
    * image新增docker進行任何動作都會產生layer 如果沒有作儲存的動作產生的layer都會消失

新增hi.txt
```
docker run -it ubuntu bash
cd /home
ls
touch "hi" > hi.txt
cat hi.txt
exit
docker run -it ubuntu bash
cd /home
ls
```
* 保存docker的變更
    * 建立一個docker，建立一個檔案，ctrl+p + ctrl+q可在不結束的狀態退出
    * 使用docker ps確認剛建的CONTAINER ID
    * docker commit [CONTAINER ID] ubuntu:v1 會建立一個TAG為v1的新鏡像，可用docker images確認
        * [CONTAINER ID] 前三位就好
    * docker attach [CONTAINER ID]可以進入目前存在的鏡像
    * 如果要刪除 docker rm  [CONTAINER ID]
    * docker run -it ubuntu:v1 bash可以看到之前建的檔案被保存下來
```
docker run -it ubuntu bash
cd /home
ls
touch "hi" > hi.txt
ls
docker ps
docker commit [CONTAINER ID] ubuntu:v1
docker attach [CONTAINER ID]
docker rm  [CONTAINER ID]
docker run -it ubuntu:v1 bash
```
* docker 除錯
    * docker pull busybox sh 在這裡可以做ping 8.8.8 or ifconfig
```
docker pull busybox
docker pull busybox sh
```
* 如果docker正在執行還想回來做其他事
    * 如果用 docker attach 會回到相同的終端
    *  docker exec -it [CONTAINER ID] sh 可以開啟其他新的終端
```
docker exec -it [CONTAINER ID] sh 
```
* 如果再執行想刪除
    * 兩種
        * 先停止再刪除
        * 直接刪除    
```
docker stop [CONTAINER ID]
docker rm [CONTAINER ID]
```
```
docker rm -f $(docker ps -a -q)
```
* docker httpd
```
docker pull httpd
docker run -d -p 8889:80 httpd
which bash
gedit docker_httpd_setup5.sh 
內容
#! /usr/bin/bash

for i in {1..5};
do
    portno=`expr 9000 + $i`
    docker run -d -p $portno:80 httpd
done
以上為內容
chomd + x docker httpd setup5.sh
./docker httpd setup5.sh
docker ps

```
* docker hub
```
docker login
輸入email 密碼
docker images
docker tfp ubuntu:v1 帳號/ubuntu:v1
docker push 帳號/ubuntu:v1
docker rmi -f [CONTAINER ID]
docker pull 帳號/ubuntu:v1
```
* docker mapping
    * docker apache的網頁主要儲存在/usr/local/apache2/htdocs#下
    * 在實體主機建立資料夾
    * docker run -d -p 8888:80 -v /home/user/web:/usr/local/apache2/htdocs httpd 把實體位置的資料夾和docker連接在一起
```
mkdir web
cd web
echo "hi" > hi.htm
ls
hi.htm
docker run -d -p 8888:80 -v /home/user/web:/usr/local/apache2/htdocs httpd
```