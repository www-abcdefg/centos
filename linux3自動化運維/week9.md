# 第九週筆記
## 用jumpserver寄信
* 步驟
    * 步驟1：進入 Google 安全設定頁面 
    * 步驟2：啟用「兩步驟驗證」 
    * 步驟3：設定「應用程式密碼」 
    * 步驟4：設定Google SMTP寄信 
        * SMTP主機：smtp.gmail.com
        * SMTP 埠號：465
        * SMTP 安全模式：SSL/TLS
        * SMTP 認證：是
        * SMTP 帳號：你的gmail帳號
        * SMTP 密碼：google應用程式密碼
```
 docker-compose -f docker-compose-redis.yml -f docker-compose-mariadb.yml -f docker-compose.yml up
```
* jumpserver 寄信
    * 登入jumpserver
    * system setting -> email -> STMP server
        * SMTP host：smtp.gmail.com
        * SMTP port：465
        * SMTP security mode：SSL/TLS
        * SMTP authentication：yes
        * SMTP account： [gmail信箱]
        * SMTP password：[google應用程式密碼]
    * 郵件服務器的SMTP帳號與發件人需一致
    * 確認結果
* 回到consoie
    * user 新增帳號
        * name:aa
        * username:aa 
        * 填入email
        * system roles :user
        * 其他皆不需要改 

## 文字三劍客
* grep用法
    *  -A<顯示行數>：除了顯示符合範本樣式的那一列之外，並顯示該行之後的內容。
    * -B<顯示行數>：除了顯示符合樣式的那一行之外，並顯示該行之前的內容。
    * -C<顯示行數>：除了顯示符合樣式的那一行之外，並顯示該行之前後的內容。
    * -c：統計匹配的行數
    * -e ：實現多個選項間的邏輯or 關係
    * -E：擴充套件的正則表達式
    * -f FILE：從FILE獲取PATTERN匹配
    * -F ：相當於fgrep
    * -i --ignore-case #忽略字元大小寫的差別。
    * -n：顯示匹配的行號
    * -o：僅顯示匹配到的字串
    * -q： 靜默模式，不輸出任何資訊
    * -s：不顯示錯誤資訊。
    * -v：顯示不被pattern 匹配到的行，相當於[^] 反向匹配
    * -w ：匹配 整個單詞
```
yum install httpd
systemctl start httpd
curl 127.0.0.1
cd /var/lod
cd httpd
more access_log
cat access_log | awk '{print $1}
cat access_log | awk '{print $1} |sort |uniq
cat access_log | awk '{print $1} |sort |uniq |wc -l
cat access_log | grep -c "[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}"
cd 
grep -r -n "root" /etc/
```
## sed
* sed用法
    * -n：不輸出模式空間內容到螢幕，即不自動列印，只列印匹配到的行
    * -e：多點編輯，對每行處理時，可以有多個Script
    * -f：把Script寫到檔案當中，在執行sed時-f 指定檔案路徑，如果是多個Script，換行寫
    * -r：支援擴充套件的正則表達式
    * -i：直接將處理的結果寫入檔案
    * -i.bak：在將處理的結果寫入檔案之前備份一份
    * 刪除空白行
        * sed -i "/^$/d" demo
## 額外資訊
* Asssets可以使用Export把資產轉換成csv檔案來看
## 參考資料
* [如何使用Google SMTP寄信(兩段式驗證+應用程式密碼) - 主機架站寄信教學](https://www.webdesigntooler.com/google-smtp-send-mail)
* [Linux文字三劍客超詳細教學---grep、sed、awk](https://tw511.com/a/01/11537.html)