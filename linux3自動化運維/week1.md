# 第一週筆記
## WabDAV
* epel用來安裝第三方軟體資料庫
* 如果有出現 下列三個則不需要再裝任何東西
    * dav_module (shared)
    * dav_fs_module (shared)
    * dav_lock_module (shared)
* 把權限給apache
    * chown -R apache:apache /var/www/html
```
su
yum install epel-relese
systemctl start httpd
systemctl status httpd
ifconfig
sudo httpd -M | grep dav
cd /var/www/html
mkdir webdav
chown -R apache:apache /var/www/html
chmod -R 755 /var/www/html
gedit /etc/httpd/conf.d/webdav.conf
systemctl restart httpd
systemctl status firewalld
getenforce
```
* gedit /etc/httpd/conf.d/webdav.conf 內容
```
DavLockDB /var/www/html/DavLock
<VirtualHost *:80>
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/webdav/
    ErrorLog /var/log/httpd/error.log
    CustomLog /var/log/httpd/access.log combined
    Alias /webdav /var/www/html/webdav
    <Directory /var/www/html/webdav>
        DAV On
    #    AuthType Basic
    #    AuthName "webdav"
    #    AuthUserFile /etc/httpd/.htpasswd
    #    Require valid-user
    </Directory>
</VirtualHost>
```
* 回到電腦本機 -> 新增網路磁碟機 -> 在資料夾位置輸入 http:// IP位置 /webdav ->在案完成即可完成
* 測試webdav
    * 新增檔案或拖拉檔案到此磁碟機後，到 虛擬機 cd /webdav 用ls檢查

## IPV6連線
* 首先先到IPV6網站查一下有無IPV6
* [IPV6](https://test-ipv6.com/index.html.zh_TW)

![pic1]()

* 虛擬機設定 -> 網路設為 橋接式的網路
* 如果要用IPV6去搜尋網址請用中括號刮起來
    * 如果埠號不再80的話在中括號後加上:埠號
* 申請網域名稱
    * [dynv6](https://dynv6.com/)
    * 如果IPV6跟虛擬機不一樣自行去修改
```
EX: http://[IPV6位置]/webdav/a.txt
```
* 

## 補充
* 正則表達式
    * -i是在這個文件進行操作，操作完資後寫入文件中(無-i不會修改內容)
    * s為取代的意思
    * ^ 以甚麼符號開頭(沒有寫則以任何開頭)、#& 取代  (&原本的內容)
    * /^/#&用後面#&的東西去取代^
    * / #皆可互換
```
 sed -i 's/^/#&/g' /etc/httpd/conf.d/welcome.conf
```
* 範例
    * 搜尋 Options Indexes FollowSymLinks 用 Options FollowSymLinks 內容取代
```
sudo sed -i "s/Options Indexes FollowSymLinks/Options FollowSymLinks/" /etc/httpd/conf/httpd.conf
``` 
## 參考資料
* [webdav](https://www.vultr.com/docs/how-to-setup-a-webdav-server-using-apache-on-centos-7/?fbclid=IwAR1RZt9cPFWrKZfjJOg4EJ6JSC7iF2i3PcAAZ6lMrsUhFmEmJoM-NuTDa2I)