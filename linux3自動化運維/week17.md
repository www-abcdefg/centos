# 第十七週
## ansable 
* project3
    * 將變數設計在playbook外，較有彈性(若要下載其他的playbook不需更改只需更改vars_public.yml的內如ex:將app2: gedit改為app2: vim)
```
cd prohect3
ansible-playbook -e app1=httpd -e app2=vsftp playbook.yml
gedit vars_public.yml
內容為
app1: wget
app2: gedit
以上為內容(若要下載其他的改app後的變數)
```
* project5
    * 修改ip
    * 會分別去安裝各自要安裝的
    * 變數一樣的可以放在servers裡面 不一樣的則放在host內
    
* project6
    * {{host}} 由命令列輸入決定
    * 命令列優先權較大
```
ansible-playbook -e host=server1 playbook.yml
```
* project8
    * 配置黨copy會因為配置黨不一樣而有所不同
* 看cpu核心數
```
ansible server1 -m setup | grep cpu
```
* 如果不是預設埠
    * 加上ansible_ssh_port=埠號
```
gedit /etc/ansible/hosts
192.168.56.101 ansible_ssh_port=220
```