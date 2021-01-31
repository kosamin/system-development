#### RDS（mysql5.7）既存バグ
- https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/UserGuide/MySQL.KnownIssuesAndLimitations.html#MySQL.Concepts.ParameterNotes

事象
REDOログにBLOBデータを出力する場合、サイズ制限がある。
innodb_log_file_sizeを、①全テーブルの中で最も大きい BLOB データサイズと、②そのテーブルの可変長フィールド (VARCHAR、VARBINARY、TEXT) のデータサイズの合計より 10 倍大きくする。
https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-20.html



## 厳密モード(STRICT_ALL_TABLES)
文字数オーバー時にエラーを発生させる。

## Unique key, primary key の違い
- Unique key : Nullを登録でき、nullは重複と見なさない
- primary key : Nullを登録できない