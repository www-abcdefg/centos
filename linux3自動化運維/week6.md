# 第六週筆記
## rsync
* rsync
    * 可以將檔案或目錄從來源位置複製到目的位置
    * -v：verbose 模式，輸出比較詳細的訊息。 (互動可使用自動化少用)
    * -r：遞迴（recursive）備份所有子目錄下的目錄與檔案。
    * -a：封裝備份模式，相當於 -rlptgoD，遞迴備份所有子目錄下的目錄與檔案，保留連結檔、檔案的擁有者、群組、權限以及時間戳記。
    * -z：啟用壓縮。
    * -h：將數字以比較容易閱讀的格式輸出。
    * rpm -qa | grep rsync 有出現rsync代表有安裝反之則沒有安裝
    * rsync -avh --delete testdir/ /tmp 如果要刪則加上--delete 把tmp所有不同的刪掉
```
yum install rsync
rpm -qa | grep rsync
echo "hi" > a.txt
rsync -avh a.txt /tmp

開新視窗
cd /tmp
ls
cat a.txt

原視窗
mkdir testdir
cd testdir
touch {1..10}.txt
cd ..
rsync -avh testdir/ /tmp

新視窗
cd /tmp
ls

原視窗
cd testdir
echo "1" > 1.txt
cd ..
rsync -avh testdir/ /tmp
cd testdir
md5sum 1.txt
echo "11" > 1.txt
md5sum 1.txt
touch 11.txt 12.txt
cd ..
rsync -avh testdir/ /tmp
cd testdir
rm 4.txt 5.txt
cd ..
rsync -avh testdir/ /tmp
rsync -avh --delete testdir/ /tmp
cd testdir
mkdir a/b/c -p
tree
cd ..
rsync -avh --delete testdir/ /tmp
```
* 遠端備份
    * 若要使用其他rsync -avzh "ssh -p 22" testdor/ user@192.168.56.101:/tmp/backup/
    * ntpdate watch.stdtime.gov.tw同部台灣時間
    * 原虛擬機為客戶端 副機為server
```
副機
su 
cd /tmp
mkdir backup

原虛擬機
ssh-keygen
ssh-copy-id user@192.168.56.101
ssh user@192.116.56.113
rsync -avzh testdor/ user@192.168.56.101:/tmp/backup/
touch testdir/111.txt
rsync -avzh testdor/ user@192.168.56.101:/tmp/backup/

副機
tree backup/

原虛擬機
cd testdir/
touch {a..c}.txt
cd ..
tree
rsync -avzh --excludr 'a.txt' --exclude'b.txt' testdor/ user@192.168.56.101:/tmp/

副機
cd /etc
ls rsyncd.conf
gedit rsyncd.conf
內容
uid=root
gid=root
pid file = /var/run/rsyncd.pid
log file = /var/log/rsyncd.log
secrets file = /etc/rsyncd.passwd

[mod1]
        path = /backup1
        read only = no
        auth users = vuser
以上為內容
gedit /etc/rsyncd.passwd
內容
vuser:123456
以上為內容
chmod 600 /etc/rsyncd.passwd
ntpdate watch.stdtime.gov.tw

原虛擬機
ntpdate watch.stdtime.gov.tw

副機
systemctl start rsync
systemctl status rsync
cat /var/run/rsync.pid
cat /var/log/rsync.log
mkdir /backup1

原虛擬機
gedit /etc/rsync_vuser.passwd
內容
123456
以上為內容
chmod 600 /etc/rsync
chmod 600 /etc/rsync_vuser.passwd
rsync -avzu --progress --password-file=/etc/rsync_vuser.passwd testdir/ vuser@192.156.56.101::mod1
```
## inotify
* m監測
* r子目錄有事件觸發
* q訊息顯示比較少
```
yum install inotify-tools
inotifywait -mrq /home/user/testdir --timefmt "%d-%m-%y %H %M"  --format "%T %w%f" -e create,modify,delete,move
主虛擬機新視窗
cd testdir
rm 6.txt  =>原視窗即可看到

gedit backup.sh
內容
#!/bin/bash
path=/home/user/testdir
server=192.168.56.111

inotifywait -mrq /home/user/testdir --timefmt "%d-%m-%y %H %M"  --format "%T %w%f" -e creat,emodify,delete,move | while read line
do
    if [ -f $line];then
    echo $line
    rsync -az --delete $line vuser@$server::mod1 --password-file=/etc/rsync vuser passed
    fi
done
./backup.sh
```

## 參考資料
* [rsync](https://blog.gtwang.org/linux/rsync-local-remote-file-synchronization-commands/)