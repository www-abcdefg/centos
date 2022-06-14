# 第十六週
## user模組
* user
    * absent 刪除
* group 群組
```
ansible ansible -m command -a "id mary"
ansible ansible -m user -a "name=mary uid=1100 state=present"
ansible ansible -m user -a "name=mary state=absent"
ansible ansible -m group -a "name=msnager state=present"
ansible ansible -m user -a "name=msnager group=msnager state=present"
```
* 上傳ansable檔案
```
cd /ansible
ls
cd project1
gedit httpd.conf playbook.yml
內容
- hosts: server1
  tasks:
    - name: install httpd server
      yum: name=httpd state=present

    - name: start httpd server
      service: name=httpd state=started enabled=yes
以上為內容
```
* 測試 
    * 到第二台虛擬機確認httpd有無先下載
    * 有則用rpm -e httpd移除
```
rpm -qa |grep http
rpm -e httpd
回到第一台虛擬機
ansible-playbook
ansible-playbook playbook.yml
curl http://centos7-2
```
* 如果要修改埠號
```
httpd.conf playbook.yml內容修改為
- hosts: server1
  tasks:
    - name: install httpd server
      yum: name=httpd state=present

    - name: configure httpd server
      copy: src=./httpd.conf dest=/etc/httpd/conf/httpd.conf

    - name: start httpd server
      service: name=httpd state=started enabled=yes
以上為內容
再去http.conf把埠號修改
```
* 重新加載配置黨
```
- hosts: server1
  tasks:
    - name: install httpd server
      yum: name=httpd state=present

    - name: configure httpd server
      copy: src=./httpd.conf dest=/etc/httpd/conf/httpd.conf
      notify: reloaded httpd server

    - name: start httpd server
      service: name=httpd state=started enabled=yes

  handlers:
    - name: restart httpd server
      service: name=httpd state=reloaded

```
## ftp
* 如果不想讓別人使用anonymous就把anonymous_enable=YES改成NO後重新啟動(playbook.yml)
```
yum install ftp
ftp centos7-2
帳號user
密碼user
cd /ansible
mkdir vsftpd-install
cd /vsftpd-install
gedit playbook.yml vsftpd.conf &
內容
- hosts: server1
  tasks:
    - name: install vsftp server
      yum: name=vsftp state=present
        
    - name: configure vsftp server
      copy: src=./vsftp.conf dest=/etc/vsftp/conf/vsftp.conf
      notify: restart vsftp server

    - name: start vsftp server
      service: name=vsftp state=started enabled=yes

  handlers:
    - name: restart vsftp server
      service: name=vsftp state=restarted
以上為內容
gedit /etc/vsftpd/vsftpd.conf
內容
anonymous_enable=YES
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=NO
listen_ipv6=YES

pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
以上為內容
ansible-playbook playbook.yml
ftp centos7-2
帳號anonymous
密碼abc@gmail.com
內容
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
xferlog_enable=YES
connect_from_port_20=YES
xferlog_std_format=YES
listen=NO
listen_ipv6=YES

pam_service_name=vsftpd
userlist_enable=YES
tcp_wrappers=YES
以上為內容
ansible-playbook playbook.yml
ftp centos7-2
帳號anonymous
密碼abc@gmail.com
```
* ftp直接創建帳號密碼
    * cd /vsftpd-install
    * gedit playbook.yml vsftpd.conf &
```
內容
- hosts: server1
  tasks:
    - name: install vsftp server
      yum: name=vsftp state=present

    - name: add a new user "mary"
      user:
        name: mary
        password: "{{'mary'|password_hash('sha512')}}"
        
    - name: configure vsftp server
      copy: src=./vsftp.conf dest=/etc/vsftp/conf/vsftp.conf
      notify: restart vsftp server

    - name: start vsftp server
      service: name=vsftp state=started enabled=yes

  handlers:
    - name: restart vsftp server
      service: name=vsftp state=restarted

```
## 參考資料
* [太厲害了，終於有人能把Ansible講的明明白白了，建議收藏](https://tw511.com/a/01/32123.html)
* [ftp](https://hoohoo.top/blog/linux-ftp-teaching/?fbclid=IwAR2CQnmb8bzEOz2uNuc9_KA6cU13NOEDYKBCE7L2gk02_3ey1VLLNZ_IKN8)