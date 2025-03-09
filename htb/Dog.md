# Dog

## Info
|||
|----|----|
|Date|2025/3/9|
|Spending time|2H|


## Research
- BackDrop
    - version
        - 1.27.1
    - Vulnerability
        - CVE-2024-41709
    - How to know
        - git-dumper
- Vulnerabilities
    - https://www.exploit-db.com/exploits/52021?utm_source=dlvr.it&utm_medium=twitter
        - http://dog.htb/?q=admin/modules/install
- hydra
    - Result
        - no login
        - block login process
- credential
    - Username
        - dogBackDropSystem
    - Password
        - [Unknown]

## Result
- ユーザ権限獲得失敗
- 認証状態で任意のコマンドを実行できるということがわかったが、どうやって認証状態に持っていくのかが不明。アカウント名は「dogBackDropSystem」っぽいが、パスワードがわからない。。。サイトの中を見て回ったが、パスワードっぽい記載も見つからないし、積んだ〜〜〜