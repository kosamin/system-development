#### エラーログ
```
アプリ環境のエラーログ
/home/tdi/APPLICATION/logs/error.log
/home/tdi/APPLICATION/logs/error.log

webサーバのエラーログ(httpd.conf)
/etc/httpd/logs/APPLICATION/error_log
/etc/httpd/logs/APPLICATION/error_log

メールのエラーログ
/var/log/maillog

mysqlのエラーログ（my.cnf）
/var/log/mysqld.log
```

#### ファイル圧縮系
絶対パスでアーカイブすると絶対パス付きで展開されるので、相対パスでアーカイブする。
```
ファイル圧縮
tar -zcvf xxxx.tar.gz application.jp

ファイル解凍
tar -zxvf xxxx.tar.gz
```
#### 検索系
```
あいまい検索
find . -name '*.php'

ファイル数検索
find . -type f | wc -l
```
