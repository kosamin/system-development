## 公開鍵設定（ユーザ）
```
鍵生成
ssh-keygen -t rsa -f holo_magazine_rsa

公開鍵をユーザホームの.sshdディレクトリに移動
mkdir ~/chanmike/.ssh/
chmod 700 .ssh
scp -i 20210222004211.pem root@118.27.5.38:~/chanmike/.ssh/

公開鍵ファイルをauthorized_keysファイルに変更
mv id_rsa.pub .ssh/authorized_keys
chmod 600 .ssh/authorized_keys

```
```
scp -i 20210222004211.pem chanmike@118.27.5.38:~/chanmike

```



## vim日本語文字化け対応
~/.vimrc に追加する。ファイルが存在しない場合は作成する。
```
##################################
# Vim起動時に、判別の優先度を定める設定
##################################

# ファイルを読み込む時の、文字コード自動判別の順番
:set fileencodings=utf-8,cp932,euc-jp,sjis


##################################
# Vim起動時に、強制する設定
##################################

# vimの内部文字コード　（これを書くと、上記の優先度設定が無視されます）
:set encoding=utf-8

# ファイルのエンコーディング（改行コードの種類）
:set fileformat=unix
```