# 第十一週
## 監控軟體
* 先改名子 
```
hostnamectl set-hostname centos7-1
```
* 綁定三台IP(記憶體至少2GB)
    * 右上角點開找到Wired Setting
    * 在Network 找到Wired下方齒輪點進去
    * IPV4 -> Manual -> 在Address地方輸入 ip ex:192.168.56.101 ->Netmask 255.255.255.0-> Gateway 輸入ip route show查尋到的
    * DNS 8.8.8.8
```
ip route show
```
* 三台修改hosts
    * 如果ping centos7-1 如果可行代表設置成功
    * 為了方便管理可以使用無密碼登入
```
gedit /etc/hosts
內容
127.0.0.1 localhost localhost.localdomin localhost4  localhost4.localdomin4
::1       localhost localhost.localdomin localhost6  localhost6.localdomin6
192.168.56.101 centos7-1
192.168.56.102 centos7-2
192.168.56.102 centos7-3
以上為內容
```
## prometheus

![prometheus pic](https://github.com/www-abcdefg/centos/blob/main/linux3%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/pic/week11.png)

* 在第一台機器下載prometheus
    * 從user目錄 進入Downloads
    * 創造prometheus 群組
    * 創造prometheus
    * 去搜尋引擎輸入ip ex 192.168.56.101:9090
    ![pic](https://github.com/www-abcdefg/centos/blob/main/linux3%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/pic/week11-1.png)

```
cd Downloads/
wget https://github.com/prometheus/prometheus/releases/download/v2.35.0/prometheus-2.35.0.linux-amd64.tar.gz
sudo groupadd prometheus
sudo useradd -g prometheus -m -d /var/lib/prometheus -s /sbin/nologin prometheus
mkdir -p /opt/module
tar xvfz prometheus-2.35.0.linux-amd64.tar.gz -C /opt/module
cd /opt/module/
chown -R prometheus.prometheus prometheus-2.35.0.linux-amd64
mv prometheus-2.35.0.linux-amd64/ prometheus
cd /usr/lib/systemd/system
gedit prometheus.service
內容
[Unit]
Description=Prometheus
After=network-online.target

[Service]
Type=simple
Restart=on-failure
ExecStart=/opt/module/prometheus/prometheus --config.file=/opt/module/prometheus/prometheus.yml --storage.tsdb.path=/opt/module/prometheus/data --storage.tsdb.retention=30d --web.enable-lifecycle --log.level=debug

[Install]
WantedBy=multi-user.targe
以上為內容
systemctl daemon-reload
systemctl start prometheus

```
* 三台機器皆下載node_exporter
```
cd /home/user/Downloads/
wget https://github.com/prometheus/node_exporter/releases/download/v1.3.1/node_exporter-1.3.1.linux-amd64.tar.gz
tar xvfz node_exporter-1.3.1.linux-amd64.tar.gz
mv node_exporter-1.3.1.linux-amd64 /opt/module/
mv node_exporter-1.3.1.linux-amd64 node_exporter
cd /usr/lib/systemd/system/
gedit node_exporter.service
內容
[Unit]
Description=node_exporter
Documentation=https://github.com/prometheus/node_exporter
After=network.target
 
[Service]
Type=simple
ExecStart=/opt/module/node_exporter/node_exporter
Restart=on-failure

[Install]
WantedBy=multi-user.target
以上為內容
systemctl daemon-reload
systemctl start node_exporter
systemctl status node_exporter
```
* 編輯prometheus.yml
    * 編輯完後去windowsn搜尋 http://IP:9090/targets/target (ex: http://192.168.56.101:9090/targets/target) 
```
cd /opt/module/prometheus
gedit /prometheus.yml
內容在- targets: ['localhost:9090']下方加上
  - job_name: 'node_exporter1'
    static_configs:
     - targets: ['192.168.48.101:9100']

  - job_name: 'node_exporter2'
    static_configs:
     - targets: ['192.168.48.102:9100', '192.168.48.103:9100']
以上為內容
systemctl restart prometheus
```

## 參考資料
* [prometheus](https://iter01.com/515175.html)
* [Prometheus学习（二）](https://www.codeleading.com/article/10525895320/)
* [下載prometheus](https://prometheus.io/download/)
