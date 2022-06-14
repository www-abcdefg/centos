# 第十五周
## Ansible
* ansible servers -m ping 先確定可以執行
* uptime 顯示開機開多久了
* 檢查節點的記憶體情況
* command
    * 想要進入資料夾執行 用chdir
    * creates=a ls 如果a存在就不會執行ls
    * removes=a ls 如果a存在才執行ls
* file
    * absent刪除
    * directory創建
* yum
    * 安裝
        *  latest 
        * present
        * installed
    * 解除安裝
        * removed
        * absent
```
ansible servers -m ping
ansible servers -m command -a "uptime"
ansible -m command -a "free -m" server1
ansible server1 -m command -a "useradd tom"
ansible server1 -m command -a "id tom"
ansible server1 -m shell -a "echo tom > tom.txt"
ansible server1 -m shell -a "cat tom.txt"
mkdir test-ansible
cd test-ansible/
ansible server1 -m command -a "which bash"
gedit a.sh
內容
#!/usr/bin/bash
date
hostname
echo "hello"
以上為內容
chmod +x a.sh
./a.sh
ansible server1 -m script -a "./a.sh"
ansible server1 -m script -a "/root/test-ansible/a.sh"
ansible server1 -m command -a "chdir=/tmp ls"
ansible server1 -m command -a "creates=a ls"
ansible server1 -m command -a "removes=a ls"
ansible server1 -m command -a "removes=a touch a"
echo "hello" > hello.txt
ansible server1 -m copy -a "src=./hello.txt dest=/tmp/hello.txt owner=user group=user mode=0644"
ansible server1 -m command -a "ls -l /tmp/hello.txt"
ansible server1 -m copy -a "content'hello world' dest=/tmp/hi.txt"
ansible server1 -m command -a "cat /tmp/hi.txt"
ansible server1 -m copy -a "content'hello world123 world' dest=/tmp/hi.txt backup=yes"
ansible server1 -m command -a "ls /tmp"
ansible server1 -m command -a "ls -l ./tmp/a.txt"
ansible server1 -m file -a "path=/tmp/a.txt owner=tom group=tom mode=0755"
ansible server1 -m command -a "ls -l ./tmp/a.txt"
ansible server1 -m file -a "path=/tmp/a.txt state=absent"
ansible server1 -m file -a "path=/tmp/testdir state=directory"
ansible server1 -m file -a "path=/tmp/abcd.txt state=touch"
ansible server1 -m stat -a "path=a.txt"
ansible server1 -m yum -a "name=httpd state=latest"
ansible server1 -m yum -a "name=httpd state=absent"
```
## 參考資料
* [太厲害了，終於有人能把Ansible講的明明白白了，建議收藏](https://tw511.com/a/01/32123.html)
