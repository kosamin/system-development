# Docker環境にCronを設定する方法
Laravelのスケジューラを使用する時、docker環境にcronを設定する必要が出てきたので、忘備録として残します。

やり方として2つ試しました。
- コンテナ内にCronを設定する方法
- ホストサーバのCronからコンテナ内のプログラムを実行する方法

## ①コンテナ内にCronを設定する方法
- コンテナにcronをインストールする
- cronを操作できるエディタをインストールする(vim等)

```
# Dockerfile
RUN apt-get update && apt-get -y install cron vim
```
- ホスト側でcrontabに設定するファイルを作成し、コンテナ内のcrontabに反映させる。

```
#custom-cron

* * * * * echo "Hello world!" >> /var/log/cron.log 2>&1
```
```
# Dockerfile

# ホスト側からコンテナ内へファイルをコピー
COPY ./custom-cron /etc/cron.d/custom-cron
RUN chmod 0644 /etc/cron.d/custom-cron

# crontabに設定を反映
RUN crontab /etc/cron.d/custom-cron
```
## ②ホストサーバのCronからコンテナ内のプログラムを実行する方法
- ホスト側のcrontabにdockerに接続するコマンド + 実行コマンドを設定する

```
# crontab

* * * * * /usr/local/bin/docker exec -t docker_app_1 php artisan schedule:run >> /dev/null 2>&1
```

## 実装中に発生したエラー
#### editorがないエラー
コンテナ内にvimを追加し解決。
```
crontab -e

no crontab for root - using an empty one
/usr/bin/sensible-editor: 25: /usr/bin/sensible-editor: editor: not found
/usr/bin/sensible-editor: 28: /usr/bin/sensible-editor: nano: not found
/usr/bin/sensible-editor: 31: /usr/bin/sensible-editor: nano-tiny: not found
/usr/bin/sensible-editor: 34: /usr/bin/sensible-editor: vi: not found
Couldn't find an editor!
Set the $EDITOR environment variable to your desired editor.
crontab: "/usr/bin/sensible-editor" exited with status 1
```
#### crontabの末尾に改行が存在しない場合エラー
LFで改行を追加し、解決。
```
new crontab file is missing newline before EOF, can't install.
```

## 参考URL
- https://qiita.com/YuukiMiyoshi/items/bb7f14436d60d4bd8a8b
- https://serverfault.com/questions/924779/docker-cron-not-working
- https://510052.xyz/posts/k78e5r2n1cty0ygv3ie5/