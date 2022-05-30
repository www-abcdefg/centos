# 第十三周
## prometheus監控httpd
```
第一台
systemctl start prometheus
systemctl start node_exporter
systemctl start pushgateway
第二台
systemctl start node_exporter
第三台
systemctl start node_exporter
第二台
cho "some_metric 6.66" | curl --data-binary @- http://192.168.56.101:9091/metrics/job/some_job
mkdir -p /app/scripts/pushgateway
cd /app/scripts/pushgateway
gedit tcp_waiting_connection.sh
內容
#!/bin/bash

# For waiting connections
label="count_netstat_wait_connetions"
count_netstat_wait_connetions=`netstat -an | grep -i wait | wc -l`
echo "$label:$count_netstat_wait_connetions"
echo "$label $count_netstat_wait_connetions" | curl --data-binary @- http://192.168.56.101:9091/metrics/job/pushgateway/instance/$instance_name
以上為內容
./tcp_waiting_connection.sh

yum install -y httpd
systemctl start httpd
curl http://192.168.56.101 > /dev/null
echo $?
cp tcp_waiting_connection.sh check_httpd_centos7-2.sh
gedit check_httpd_centos7-2.sh
內容
#!/bin/bash

# For waiting connections
label="httpd_status_centos7_2"
curl -q  http://192.168.157.136 >/dev/null 2>&1
status=`echo $?`
if [ $status -eq 0 ];then
   result=1
else
   result=0
fi
#echo $label $result
echo "$label $result" | curl --data-binary @- http://192.168.56.101:9091/metrics/job/$label
以上為內容
./check_httpd_centos7-2.sh
systemctl start httpd
./check_httpd_centos7-2.sh
crontab -e
內容
* * * * * /app/scripts/pushgateway/check_httpd_centos7-2.sh
* * * * * sleep 15;/app/scripts/pushgateway/check_httpd_centos7-2.sh
* * * * * sleep 30;/app/scripts/pushgateway/check_httpd_centos7-2.sh
* * * * * sleep 45;/app/scripts/pushgateway/check_httpd_centos7-2.sh
以上為內容
crontab -l
```
## Grafana繪圖
* 左側+的符號選到Dashboard -> add a new panel
* 找到 Data source選擇 Prometheus如下圖

![Grafana繪圖]()

* 找到query的Meteics browser 輸入 httpd_status_centos7_2 在案Run query 

![Grafana繪圖1]()

* 點選save進行儲存 就可形成圖表
