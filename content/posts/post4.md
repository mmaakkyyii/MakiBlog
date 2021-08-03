---
title: "myDNSのIP通知の自動化"
date: 2021-08-03T12:17:00+09:00
draft: false
tags: ["blog","systemd"]
---

# はじめに
ブログ用にDNSは[myDNS](https://www.mydns.jp/)を利用している。  
これを利用する際には定期的にIPアドレスの通知をする必要がある。  

コマンドライン上からIPの通知をするなら
```
curl -u {mydnsID}:{Password} https://ipv4.mydns.jp/login.html
```
これで通知をだせるのでこれを毎日実行するなどすればおｋ

# systemd
シェルスクリプトにIP通知するシェルを追加  
/opt/myDNSIP/bin/IpNotification.sh  
に
```sh
#!/bin/sh
curl -u {mydnsID}:{Password} https://ipv4.mydns.jp/login.html
```
これに権限を追加
```
sudo chmod 755 /opt/myDNSIP/bin
sudo chmod 755 /opt/myDNSIP/bin/IpNotification.sh
```

次のファイルを追加  
/etc/systemd/system/myDNSIPNotification.service
```
[Unit]
Description = myDNS IP notification
After=local-fs.target
ConditionPathExists=/opt/myDNSIP/bin

[Service]
ExecStart=/opt/myDNSIP/bin/IpNotification.sh  
Restart=no
Type=simple

[Install]
WantedBy=multi-user.target
```

/etc/systemd/system/myDNSIPNotification.timer
```
[Unit]
Description=timer exec.sh

[Timer]
OnUnitActiveSec=24hour

[Install]
WantedBy=timers.target
```

権限追加
```
sudo chmod 644 /etc/systemd/system/myDNSIPNotification.service
sudo chmod 644 /etc/systemd/system/myDNSIPNotification.timer
```
有効化
```
sudo systemctl daemon-reload
sudo systemctl start myDNSIPNotification.service
sudo systemctl start myDNSIPNotification.timer
sudo systemctl enable myDNSIPNotification.timer
```
