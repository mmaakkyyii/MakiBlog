---
title: "推しの事務所プロフィールの更新を通知するTwitter Botを作る"
date: 2022-05-28T17:56:27+09:00
draft: false
categories: ["その他"]
tags: ["オタク","Twitter","systemd"]
---

# はじめに
河野ひよりさんの事務所のHPの出演情報を通知するTwitter Botが必要とされているので[[1]()]これを作る

# やること
ブログにも利用しているRaspberry Pi Zero上で動くPythonスクリプトを作成して、毎日実行する。  
PythonスクリプトではプロフィールページのHTMLファイルを取得してこれを以前のHTMLファイルと比較して差分があればその部分をツイートして、ローカルに最新版のHTMLファイルを保存しておきます。

# HTMLファイルの取得と差分の抽出
requestsでURLを指定するとHTMLファイルを取得することができるのでこれを使ってHTMLファイルをダウンロードします。 
またdifflibを使って古いファイルと最新のファイルの差分を取得します。もし差分があればその部分をツイートして、さらに最新版のHTMLファイルとしてローカルにも保存するようにします。

必要部分のコードを以下に示します。
```Python
import requests
import difflib
import os
import shutil

piyo_URL="https://firstwind.co.jp/talentpage/kono_hiyori"
response = requests.get(piyo_URL)

temp_file="temp_file.html"
with open(temp_file,"w") as f:
    f.write(response.text)

ls=os.listdir(path='html')
print(ls)

old = open('html/'+ls[-1]) #lsは名前順で日付順になっているため新しいファイルは配列の一番最後に格納されている
new = open(temp_file)

diff = difflib.Differ()

output_diff = diff.compare(old.readlines(),new.readlines())
diff_line_num=0

for o in output_diff:
    if o[0:1] in ['+','-']:
        print(o)
        if len(o)>139-len(piyo_URL): #差分の行が長すぎるとツイートできないので後ろ側を削除
            o=o[0:139-len(piyo_URL)]
        tweet_text=o+'\n'+piyo_URL
        diff_line_num=diff_line_num+1
        tweet(tweet_text)

if diff_line_num>0: #差分があったときのみ最新版のHTMLファイルとして保存する
    shutil.copy(temp_file,'html/'+today_file)
else:
    print('[DEBUG]No difference')

old.close()
new.close()  
```
# Twitter API
ライブラリはTweepyを使います。インストールは
```
pip3 install tweepy
```
でおｋ
## Twitter Developer Platform
botとして利用するアカウントで https://developer.twitter.com/en にログインします。  
新規プロジェクトとアプリを作成してAPIキーを取得する(ちゃんと記録を取っていなかったので詳しい説明は省略)  
デフォルトのままだとツイートする権限が足りないと言われるので、OAuth 1.0aにチェックを入れてRead and writeにする。  
![](../img/twitter_auth.JPG)
以下のPythonスクリプトではアクセストークンなどを下の画面から生成したものを利用する。
![](../img/twitter_access_token.JPG)
ツイート部分のコードはこんな感じ

```Python
import tweepy

api_key = "(ここにAPI key)"
api_secrets = "(ここにAPI secrets))"
bearer_token = "(ここにBearer token)"
access_token = "(ここにaccess token)"
access_secret = "(ここにaccess secret)"


auth = tweepy.OAuthHandler(api_key,api_secrets)
auth.set_access_token(access_token,access_secret)
api = tweepy.API(auth ,wait_on_rate_limit = True)


def tweet(text):
    if len(text)>140:
        text=text[0:139]
    api.update_status(status=text)

```

# できたもの
フォルダ構成は以下の通りです。  
diff_piyo_bot/  
├── diff_piyo.py  
├── html  
│   ├── 2022_5_24_kono_hiyori.html  
│   └── 2022_5_25_kono_hiyori.html  
└── temp_file.html  

``` Python
#!/usr/bin/python3

import requests
import difflib
import datetime
import os
import shutil
import tweepy

api_key = "(ここにAPI key)"
api_secrets = "(ここにAPI secrets))"
bearer_token = "(ここにBearer token)"
access_token = "(ここにaccess token)"
access_secret = "(ここにaccess secret)"


auth = tweepy.OAuthHandler(api_key,api_secrets)
auth.set_access_token(access_token,access_secret)
api = tweepy.API(auth ,wait_on_rate_limit = True)


def tweet(text):
    if len(text)>140:
        text=text[0:139]
    api.update_status(status=text)

def main():

    now = datetime.datetime.now()
    today=str(now.year)+'_'+str(now.month)+'_'+str(now.day)+'_'
    print(today)
    piyo_URL="https://firstwind.co.jp/talentpage/kono_hiyori"
    response = requests.get(piyo_URL)

    print('[DEBUG] Status code : '+str(response.status_code))    # HTTPのステータスコード取得
    today_file=today+'kono_hiyori.html'

    temp_file="temp_file.html"
    with open(temp_file,"w") as f:
        f.write(response.text)

    ls=os.listdir(path='html')
    print(ls)

    old = open('html/'+ls[-1])
    new = open(temp_file)

    diff = difflib.Differ()

    output_diff = diff.compare(old.readlines(),new.readlines())
    diff_line_num=0

    for o in output_diff:
        if o[0:1] in ['+','-']:
            print(o)
            if len(o)>139-len(piyo_URL):
                o=o[0:139-len(piyo_URL)]
            tweet_text=o+'\n'+piyo_URL
            diff_line_num=diff_line_num+1
            tweet(tweet_text)

    if diff_line_num>0:
        shutil.copy(temp_file,'html/'+today_file)
    else:
        print('[DEBUG]No difference')

    old.close()
    new.close()


if __name__ == '__main__':
    try:
        main()
    except Exception as e:
        print("[ERROR]")
        print(e)
        tweet('@makyi_rie '+str(e))

```
Python側のエラーがあれば自分に通知を飛ばすようにしています。

# systemdによるスクリプトの自動実行
[ここ](posts/post4/)でやっていることとほとんど同じことをします。

/etc/systemd/system/myDiffPiyo.service
```
[Unit]
Description = diff piyo
After=local-fs.target

[Service]
ExecStart=/bin/bash -c "cd /home/makyi/diff_piyo_bot"; /usr/bin/python3 diff_piyo.py
Restart=no
Type=simple

[Install]
WantedBy=multi-user.target
```
/etc/systemd/system/myDiffPiyo.timer
```
[Unit]
Description=timer for diff piyo

[Timer]
OnUnitActiveSec=24hour

[Install]
WantedBy=timers.target
```
という2つのファイルを作成して、権限を追加。
```
sudo chmod 644 /etc/systemd/system/myDiffPiyo.service
sudo chmod 644 /etc/systemd/system/myDiffPiyo.timer
```
自動起動させるために以下のコマンドを実行する。
```
sudo systemctl daemon-reload
sudo systemctl start myDiffPiyo.service
sudo systemctl start myDiffPiyo.timer
sudo systemctl enable myDiffPiyo.timer
```
# こんな感じで動いてくれる(はず)
{{</*tweet 1529473148789219333*/>}} 
HTMLの変更された行をそのままツイートするのでタグが残ったりしているが、サンプルボイスなど他にも変更される可能性がある部分はあると思うのでこのままにしておきます。