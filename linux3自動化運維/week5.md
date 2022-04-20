# 第五週筆記
## 用腳本建立多的http伺服器與主機掛載
* 建立五個目錄
```
mkdir web1 web2 web3 web4 web5
cd web1
gedit hi.htm   內容為1
cd ..
cd web2
gedit hi.htm   內容為2
cd ..
cd web3
gedit hi.htm   內容為3
cd ..
cd web4
gedit hi.htm   內容為4
cd ..
cd web5
gedit hi.htm   內容為5
cd ..
```
* 腳本
    * 檔案位置在user下
    * -p如果不存在就去創 
```
gedit prepare_web.sh
prepare_web.sh內容為
#!/user/bin/bash

for i in {1..5};
do
    mkdir -p myweb$i
    cd myweb$i
    echo $i > hi.htm
    cd ..
done
以上為內容
chmod +x prepare_web.sh
./prepare_web.sh
ls
gedit docker_httpd_setup5.sh
docker_httpd_setup5.sh內容
#!/user/bin/bash
for i in {1..5};
do
    portno='expr 9000 + $i'
    docker run -d -p $portno:80 -v /home/user/myweb$i:/usr/local/apache2/htdocs httpd
done
./docker_httpd_setup5.sh
docker ps -a
最後去搜尋引擎輸入 IP:9001/hi.htm 看是否成功
```
## dockerfile
* dockerfile
    * 檔案位置在user下
    * FROM centos:centos7 以cemtos為基礎
    * RUN yum -y install httpd 鏡像安裝
    * .本地目錄  -t 標籤名稱
```
gedit Dockerfire
內容
FROM centos:centos7
RUN yum -y install httpd
EXPOSE 80
ADD index.html /var/www/html
以上為內容
echo "hello world " > index.html
docker build -t myhttpd:1.0 .
docker images
docker run -d -p 8888:80 my httpd:1.0 /usr/sbin/apachectl -DFOREGROUND
docker ps 檢查有無跑起來
```
## docker haproxy
* docker haproxy
    * 192.16.56.113:9001(此處填自己IP)
    * 如出現 is alreadyin use...就代表之前有使用過
```
gedit haproxy.cfg
內容
defaults
    mode http
    timeout client 10s
    timeout commect 5s
    timeout server 10s
    timeout http-request 10s

frontend myfrontend
    bind 0.0.0.0:8080
    default_backend myservers

backend myservers
    balance roundrobin
    server server1 192.16.56.113:9001
    server server2 192.16.56.113:9002
    server server3 192.16.56.113:9003
    server server4 192.16.56.113:9004
    server server5 192.16.56.113:9005
以上為內容
docker run -p 8080:8080  -d --name haproxy-master -v /home/user/haproxy.cfg:/usr/local/etc/haproxy/haproxy.cfg --privileged=true haproxy
df5447cc3978605df9cfa1cbf0f924546f68c8389c4306b2fee66278ef5dc9d8
docker ps
最後去搜尋引擎輸入 IP:8080/hi.htm 看是否成功
```
## 用docker下載python3
* 到docker hub找python3
* 檔案位置在user下
```
docker pull faucet/python3
docker run -it -v /home/user:/tmp faucet/python3 bash
cd /mydata
python3 test.py
```
## AWK
* 檔案位置在user下
```
gedit file
內容
11 dywang 81 12 A
152 linda 90 58 C
33 peter 72 95 C
4 rite 65 34 E
58 cora 5 85 D
以上為內容
gedit process.awk 
內容
BEGIN{
  sum=0;
  i=0;
}
{
  sum+=$3;
  i++;
}
 
END { 
  printf("總和為:%d\n", sum);
  printf("average=%.2f\n", sum/i);
}
以上為內容
awk -f process.awk  file1
總和為:313
average=62.60
```
## 參考資料
* [awk](https://noootown.com/awk-useful-usage/)