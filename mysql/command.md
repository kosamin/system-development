#### mysqldump
```
データベース(全テーブル)に対してdumpファイルを作成
mysqldump -u rdsmaster -p -h tlcdb02.cnzceeic6fvb.ap-northeast-1.rds.amazonaws.com real_estate > `date +%Y%m%d_Database.dump`


テーブルに対してdumpファイルを作成
mysqldump -uroot -p member_table > `date +%Y%m%d_Database.dump`
mysqldump -urdsmaster -p -h tlcdb02.cnzceeic6fvb.ap-northeast-1.rds.amazonaws.com mfs > `date +%Y%m%d_Database.dump`


dumpファイルを適用
mysql -uroot -p --default-character-set=utf8 oguri< 20200226_Database.dump
```


#### テーブル操作
```
テーブルにカラム追加
alter table mfs.receivemails add household_income text after income;
alter table real_estate.custom_form_table add mfshp_flg int after mfsportal;
```

#### 実行中のクエリ一覧を表示
````
SHOW PROCESSLIST;
SELECT * FROM information_schema.PROCESSLIST WHERE TIME > 59;
SELECT * FROM information_schema.PROCESSLIST where id = 504;

クエリの実行を中断（トランザクションを終了させる）
KILL CONNECTION 557;
mysqladmin kill 1,2,3 -h localhost -u hoge
````

#### mysqlパラメータ確認
```
SHOW GLOBAL VARIABLES LIKE 'innodb_buffer_pool_%';
```

#### ユーザ名、ホスト名を確認
```
select user, host from mysql.user;
```
#### 権限の確認
```
show grants for 'ユーザー名'@'ホスト名'
```
#### 権限の付与
権限のところにはselect、update、insert、delete、の組み合わせやallなどを記述。  
- グローバルレベル：GRANT 権限 ON *.* TO user;
- データベースレベル：GRANT 権限 ON db_name.* TO user;
- テーブルレベル：GRANT 権限 ON db_name.table_name TO user;
- カラムレベル：GRANT 権限 (カラム1, カラム2, ...) ON db_name.table_name TO user;