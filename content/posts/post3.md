---
title: "GitHub Actionsでpushした記事をラズパイに投げる"
date: 2021-07-31T22:07:00+09:00
draft: false
categories: ["blog"]
tags: ["hugo","blog","GitHub Actions"]
---

# はじめに
いままでは書いた記事をラズパイ上にクローンしてきてhugoビルドをして記事が反映されるようになっていた。しかしGitHubにpushしたときに即座に記事が反映されて欲しいのでGitHub Actionsというものを使ってみる。  
hugoフォルダをGitHubにpushしたらHugoのインストールビルドを行って出来たpublicフォルダをラズパイに転送する
(つまりラズパイにhugoをインストールする必要はなかったんですね…)

# やったこと
## SSH周り
今までは基本的にLAN内でラズパイに接続していたけど外からアクセスできるようにSSH用のポート解放をして、公開鍵をラズパイ上に置いておく  
https://tool-lab.com/raspi-key-authentication-over-ssh/　ここら辺を参考に
手元のPC上で
```
ssh-keygen -t rsa
```
これでできたhoge.pubをラズパイ側の .ssh/フォルダに転送する
ラズパイ側で
```
cat .ssh/hoge.pub > .ssh/key
chmod 600 .ssh/key
rm .ssh/hoge.pub
```
これで公開鍵をラズパイ側へ転送完了

sshの設定は  
/etc/ssh/sshd_config
```
RSAAuthentication    yes
PubkeyAuthentication yes
AuthorizedKeysFile   %h/.ssh/key
PasswordAuthentication no
```
へ変更して、公開鍵認証を有効にして公開鍵のファイルを指定する。
またパスワード認証の無効化を行う。

```
sudo /etc/init.d/ssh restart
```
で設定を反映させる。

この状態で、まず自分のPCからラズパイ側にファイル転送できるか確認する。

```
rsync -rvP -e 'ssh -p {port_num} -i .ssh/raspi_ssh_key' ./test/ {user_name}@{host}:test
```
としてファイル転送ができることを確認する。
rsyncコマンドのオプションは https://www.atmarkit.co.jp/ait/articles/1702/03/news020.html ここら辺を参照
ローカルのtestフォルダをラズパイの~/testと同期する

## GitHub Actions
![](../github_actions.JPG)
こちらのActionsより今回はSimple workflowを選択

```yml

name: deploy test
on:
  push:
    branches: [ main ]
  workflow_dispatch:

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v2
        with:
          submodules: true
      - name: Setup hugo
        run: |
            wget https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_${HUGO_VERSION}_Linux-64bit.deb
            sudo dpkg -i hugo_${HUGO_VERSION}_Linux-64bit.deb
            hugo version
        env:
            HUGO_VERSION: '0.83.1'
      - name: Build hugo
        run: hugo
      - name: make key
        run: echo "$SSH_KEY" > key && chmod 600 key
        env: 
            SSH_KEY:  ${{secrets.SSH_KEY}}
      - name: deploy
        run: rsync -avP -e "ssh -i ./key -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null -p ${SSH_PORT} " ./public ${SSH_USER}@${SSH_IP}:blog/MakiBlog
        env: 
            SSH_PORT: ${{secrets.SSH_PORT}}
            SSH_USER: ${{secrets.SSH_USER}}
            SSH_IP :  ${{secrets.SSH_IP}}
```
こんな感じのymlファイルを作成
やっていることは、
・いまのリポジトリにアクセスできるようにチェックアウトする。  
・hugoをインストールする。  
・ビルドする。  
・ssh接続用のkeyを生成する。  
・ビルドして出来たpublicファイルをラズパイと同期させる。  
という流れ。  
envのsecrets.～はGitHubのリポジトリのSettings->Secretsから非公開の変数を追加できる。
ポート番号とかユーザー名とかいろいろをここに置いておく。

### 参考
https://blog.oino.li/posts/diarywithgithubactions/  
やっていることはほとんど同じでデプロイ先がちょっと違うだけ

