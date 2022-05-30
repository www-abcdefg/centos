#  第十四週
## 先啟動prometheus grafana
```
第一台
systemctl start prometheus
systemctl start node_exporter
systemctl start pushgateway
第二台
systemctl start node_exporter
第三台
systemctl start node_exporter
第一台
systemctl start docker
docker run -d --name=grafana -p 3000:3000 grafana:7.5.15
```
* 開啟瀏覽器輸入http://IP:3000登入，預設的帳號密碼都是admin
* 左側齒輪(configuration)找到 Data source選擇 
* 再點選Add data source
![Grafana1]()

* 點選Prometheus

![Grafana2]()


* 在http 的URL 輸入http://192.168.56.101:9090(其他皆為預設)
* 點選 save&test 確定是否成功
* 至windows瀏覽器輸入[Grafana](https://grafana.com/) -> Products -> Dashboards -> Node Exporter Full
* 回到http://IP:3000 點選 Create->import 在import via grafana.com 那欄輸入1860 按 Load
* Prometheus選擇來源Prometheus 再點選import就會出現面板

* 左側+的符號選到Dashboard -> add a new panel
* 找到 Data source選擇 Prometheus如下圖

![Grafana繪圖]()

* 找到query的Meteics browser 輸入 httpd_status_centos7_2 在案Run query 

![Grafana繪圖1]()

* 點選save進行儲存 就可形成圖表

## Grafana警報與line連結
* 先登入[line](https://notify-bot.line.me/zh_TW/)
* 創建群組 -> 邀請好友(Line Notify)
* 在網頁的line找到 -> 管理登入服務 -> 發行權杖 -> 複製權杖 
* 左側到鈴鐺圖示(Alerting) 選擇 Notification channels -> add channel->type 下拉選單選擇 LINE 把權杖輸入至Token name 輸入 LineNotify ->  按test 看是否跳出訊息 跳出則為成功

![Grafana警報]()

* 按 search 找到 剛才創建的面板 點選進入 後再點選Edit 

![Grafana警報1]()

* 進入Edit後 找到Alert -> Create Alert
* WHEN 選擇last() OF query(A,10s,now) IS BELOW 0.9  
* Message 選擇 LineNotify 

![Grafana警報2]()

## Ansible
* 先做無密碼登入，方便管理及維護
* 只有主控端需要安裝Asible
* 另外兩台要先確保sshd有開啟
*  ansible-doc -l 列出所有模組列表
* -v	詳細模式，如果執行成功，輸出詳細結果
* -i	指定host檔案路徑，預設在/etc/ansible/hosts
* -f,-forks=NUM	NUM預設是整數5，指定fork開啟同步程序的個數
* -m	指定使用的module名稱，預設command模組
* -a	指定模組的引數
* -k	提示輸入SSH密碼，而不是使用基於ssh金鑰認證
* -sudo	指定使用sudo獲取root許可權
* -K	提示輸入sudo密碼
* -u	指定行動端的執行使用者
* -C	測試命令執行會改變什麼內容，不會真正的去執行
* ansible servers -m command -a "date" 時間同步
```
yum install epel-release -y
yum install ansible
第二三台
systemctl status sshd
第一台
gedit /etc/ansible/hosts
內容最下方加上
[server1]
192.168.56.102

[server2]
192.168.56.103

[servers]
192.168.56.102
192.168.56.103
以上為內容
ansible server1 -m ping
ansible server2 -m ping
ansible servers -m ping
ansible-doc -l
第三台
date -s 20220101
第一台
ansible servers -m command -a "date"
```


