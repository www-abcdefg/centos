# 第十二周
## prometheus
* 先啟動第一台的prometheus  node_exporter
    * 另外兩台的 node_exporter
```
systemctl start prometheus
systemctl start node_exporter
```
* proqml
    * up{ instance=~"Ip:埠號*"} : =~政則表達式，.*:表示匹配全部!=~:正則表達式取反
    * graph: 圖形化介面
    * offset 5m 代表五分鐘前回推
```
 node_load1{instance="ip:埠號"} [1m] offset 5m  
```
* prometheus cpu模式
    * us：用户進程使用cpu的時間
    * sy：内核進程使用cpu的時間
    * ni：用户進程空間內改變過優先級的進程使用的cpu時間
    * id：空閒（没人用）的cpu時間
    * wa：等待io的cpu時間
    * hi：硬中断的cpu時間
    * si：軟中断的cpu時間
    * st：虛擬機管理程序使用的cpu時間

## Grafana(繪圖報紙)
* 開啟瀏覽器輸入http://IP:3000登入
    * 預設的帳號密碼都是admin

![grafana](https://github.com/www-abcdefg/centos/blob/main/linux3%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/pic/grafana.png)
```
cd /etc/yum.repos.d/
gedit grafana.repo
內容
[grafana]
name = grafana
baseurl = https://packages.grafana.com/oss/rpm
repo_gpgcheck = 1
enabled = 1
gpgcheck = 1
gpgkey = http://packages.grafana.com/gpg.key
sslverify = 1
sslcacert = /etc/pki/tls/certs/ca-bundle.crt
以上為內容
yum install grafana
systemctl daemon-reload
systemctl start grafana-server
systemctl status grafana-server
```
## Grafana 和 Prometheus 聯繫
* 先點左側齒輪(configuration)
* 再點選Add data source

![Grafana1](https://github.com/www-abcdefg/centos/blob/main/linux3%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/pic/grafana1.png)

* 點選Prometheus

![Grafana2](https://github.com/www-abcdefg/centos/blob/main/linux3%E8%87%AA%E5%8B%95%E5%8C%96%E9%81%8B%E7%B6%AD/pic/grafana2.png)

* 在http 的URL 輸入http://192.168.56.101:9090(其他皆為預設)
* 點選 save&test 確定是否成功
* 至windows瀏覽器輸入[Grafana](https://grafana.com/) -> Products -> Dashboards -> Node Exporter Full
* 回到http://IP:3000 點選 Create->import 在import via grafana.com 那欄輸入1860 按 Load
* Prometheus選擇來源Prometheus 再點選import就會出現面板

## pushgateway
* 最後在windows瀏覽器輸入http://IP:9090，就可以看到pushgateway
```
cd /home/user/Downloads/
wget https://github.com/prometheus/pushgateway/releases/download/v1.4.2/pushgateway-1.4.2.linux-amd64.tar.gz
mv pushgateway-1.4.2.linux-amd64.tar.gz pushgateway
mv pushgateway /opt/module/
cd /opt/module/
gedit /usr/lib/systemd/system/pushgateway.service
內容
Description=pushgateway
After=network.target

[Service]
Type=simple
ExecStart=/opt/module/pushgateway/pushgateway 
Restart=on-failure

[Install]
WantedBy=multi-user.target
以上為內容
systemctl start pushgateway
systemctl status pushgateway
gedit /opt/module/prometheus/prometheus.yml
內容 在最下方加上
- job_name: 'pushgateway'
    honor_labels: true
    static_configs:
      - targets: ['192.168.56.101:9091']
以上為內容
systemctl daemon-reload
systemctl restart prometheus
```
## 參考資料
* [Prometheus 查询语言 PromQL 的 CPU 使用率计算方法](https://blog.csdn.net/qq_35753140/article/details/105121525)
* [Prometheus监控CPU,Memory,Disk](https://www.modb.pro/db/43636)
* [在 CentOS 7 上安裝 Grafana 6.0.2](http://max043.blogspot.com/2019/03/centos-7-grafana-602.html)