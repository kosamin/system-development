
#### phpinfoパラメータ確認
```
php -r 'phpinfo();'
php -r 'phpinfo();' | grep memory_limit
```

#### 改行コード
| 改行コード | コード値 |用途 |
| :---:    | :---: | :---: |
| LF       | \n    |Unix系OS全般|
| CR+LF    | \r\n  |Windows系OS|
| CR       | \r    |古いMac OS（9以前|

#### 文字コード変換
```
echo '●変更前の文字コード：', mb_internal_encoding(), PHP_EOL;
// 文字コードを変更する
mb_internal_encoding('SJIS-Win');
echo '●変更後の文字コード：', mb_internal_encoding(), PHP_EOL;
```

#### 文字検索
```
$sentence = '今日は、良い日です';
var_dump(mb_strpos($sentence, '日'));       // 結果：1
var_dump(mb_strpos($sentence, '無'));       // 結果：false
var_dump(mb_strpos($sentence, '良い日'));   // 結果：4
var_dump(mb_strrpos($sentence, '日'));      // 結果：6

if (mb_strpos($sentence, '日')) {
    echo '引数$sentenceの中に「日」が見つかりました。', PHP_EOL;        // こちらのブロックに入ります。
} else {
    echo '引数$sentenceの中に「日」は見つかりませんでした。', PHP_EOL;
}
```

#### 文字を切り取る
```
$sentence = '今日は,良い日です';
var_dump(mb_substr($sentence, 4, 3));       // 結果：良い日
var_dump(mb_substr($sentence, -5, 3));      // 結果：良い日
var_dump(mb_substr($sentence, 4));          // 結果：良い日です

var_dump(mb_strstr($sentence, '良', false));    // 結果：良い日です
var_dump(mb_strstr($sentence, '日', false));    // 結果：日は,良い日です
var_dump(mb_strstr($sentence, '日', true));     // 結果：今
```


#### 文字の置換
```
$sentence = '今日は、良い日です';
var_dump(str_replace('今日', 'あした', $sentence)); // 結果：あしたは、良い日です

$sentences = <<< TEXT
いろはにほへと　ちりぬるを
わかよたれそ　つねならむ
TEXT;
var_dump(str_replace(["\r\n", "\r", "\n"], '', $sentences)); // 改行が除去された$sentencesを出力
```
#### 文字の分割
```
$today = '2019-03-16';

// ハイフンで分割した結果を、そのまま配列で受け取る例
$dateElements = explode('-', $today);
print_r($dateElements); // 結果：Array([0] => 2019 [1] => 03 [2] => 16)

// ハイフンで分割した結果を、list()でスカラー変数に分けて受け取る例
list($year, $month, $day) = explode('-', $today);
echo '年：', $year,     PHP_EOL;
echo '月：', $month,    PHP_EOL;
echo '日：', $day,      PHP_EOL;
```
#### 文字のパディング（穴追加/穴埋め）
```
// 連番を元に7桁のコード値を生成する例
$sequence = 1234;
$code = str_pad(strval($sequence), 7, '0', STR_PAD_LEFT);  // 結果：0001234
var_dump($code);

// 固定長電文フィールド用にスペースを埋めて8桁にする例
$price = 920;
$priceField = str_pad(strval($price), 8, ' '); // 結果：「920     」
var_dump($priceField);
```
#### 文字状態を変換（大文字/小文字/半角/全角）
```
$greetings = 'こんにちは　コンニチハ　ｺﾝﾊﾞﾝﾊ　Ｈｅｌｌｏ　ＨＥＬＬＯ　HELLO　hello　１２３';
echo mb_convert_case($greetings, MB_CASE_UPPER), PHP_EOL; // 英語が全て「HELLO」になる（英数字を大文字に変換）
echo mb_convert_case($greetings, MB_CASE_LOWER), PHP_EOL; // 英語が全て「hello」になる（英数字を小文字に変換）
echo mb_convert_case($greetings, MB_CASE_TITLE), PHP_EOL; // 英語が全て「Hello」になる(先頭の文字だけ大文字に変換)

echo mb_convert_kana($greetings, 'Hc'), PHP_EOL; // 全角カタカナ、半角カタカナを全角ひらがなに変換
echo mb_convert_kana($greetings, 'rns'), PHP_EOL; // 英数字全てを半角英数字に変換
echo mb_convert_kana($greetings, 'KV'), PHP_EOL; // 「ｺﾝﾊﾞﾝﾊ」が「コンバンハ」になる

```
#### 文字コード変換
- mb_convert_encoding  
単一の文字列データを変換する。変換元のデータを書き換えず、変換後のデータを戻り値とする。
- mb_convert_variables  
配列やオブジェクト型のデータも変換できる。元のデータを上書きする。

```
$books = file('books.txt');

// books.txtの文字コードがShift-JISのため、以下のようにそのまま出力すると文字化けします。
// var_dump($books);

foreach ($books as $book) {
    // mb_convert_encodingで、1行ずつUTF-8に変換すると文字化けしません。
    echo mb_convert_encoding($book, 'UTF-8', 'SJIS-win'), PHP_EOL;
}

// または、mb_convert_variablesで、配列全体をUTF-8に変換することもできます。
mb_convert_variables('UTF-8', 'SJIS-win', $books);
foreach ($books as $book) {
    echo $book, PHP_EOL;
}
```
#### 文字を丸める
```
$item = '万能MIXER―ご自宅でかんたんにジュースやスムージー、なんとふりかけまで！';
echo mb_strimwidth($item, 0, 20, '…'); // 結果：「万能MIXER―ご自宅で…」
```
#### 除去（スペース/改行コード/タブ）
```
// 前後にスペースがある文字列をトリムする
$greeting = ' こんにちは ';
var_dump(trim($greeting));

// 前後にスペース・タブ・改行コードがある文字列をトリムする
$greeting = " こんにちは\t\r\n";
var_dump(trim($greeting));
```
#### 文字エンコード・デコード
```
$string = 'こんにちは';

$encoded = base64_encode($string);
echo '●文字列をエンコードしました。', PHP_EOL;
echo $encoded, PHP_EOL;

$decoded = base64_decode($encoded);
echo '●文字列をデコードしました。', PHP_EOL;
echo $decoded, PHP_EOL;

$binaryImage = file_get_contents(dirname(__FILE__) . '/files/flower.png');
$encodedImage = base64_encode($binaryImage);
echo '●バイナリデータをエンコードしました。', PHP_EOL;
echo $encodedImage, PHP_EOL;

file_put_contents(dirname(__FILE__) . '/files/decoded.png', base64_decode($encodedImage));
echo '●バイナリデータをデコードして、files/decoded.pngに保存しました。', PHP_EOL;
```
#### 文字列をURLに対応する
```
// URL上問題ない文字列
echo '●「TestString」をURLエンコードします。', PHP_EOL;
$encoded = urlencode('TestString');
echo $encoded, PHP_EOL;
$decoded = urldecode($encoded);
echo $decoded, PHP_EOL;

// 日本語はURLに含めることができません
echo '●「東京都杉並区」をURLエンコードします。', PHP_EOL;
$encoded = urlencode('東京都杉並区');
echo $encoded, PHP_EOL;
$decoded = urldecode($encoded);
echo $decoded, PHP_EOL;

// 一部の半角記号もURLに含めることができません
echo '●「Mark Of !?(^^)」をURLエンコードします。', PHP_EOL;
$encoded = urlencode('Mark Of !?(^^)');
echo $encoded, PHP_EOL;
$decoded = urldecode($encoded);
echo $decoded, PHP_EOL;
```
#### URL解析
```
// 本プログラム処理は、変数$urlから「debug=1」のクエリ文字列を除去した新しいURLを生成します。

// 対象となるURL
$url = 'https://example.com/dir1/dir2/search.php?freeword=cooking&categories[0]=books&categories[1]=dvd&debug=1';

// 手順1. parse_url関数で、URLを要素に分解します。
$urlElements = parse_url($url);
echo '●URLのパース結果：', PHP_EOL;
print_r($urlElements);

// 手順2. parse_str関数で、クエリ文字列を解析します。
$queries = [];
parse_str($urlElements['query'], $queries);
echo '●クエリ文字列のパース結果：', PHP_EOL;
print_r($queries);

// 手順3. クエリ文字列から、debug=1を除去し、http_build_query関数でクエリ文字列を生成しなおします。
unset($queries['debug']);
$newQuery = http_build_query($queries);
echo '●新しく生成されたクエリ文字列：', PHP_EOL;
print_r(urldecode($newQuery));
echo PHP_EOL;

// 手順4. 新しいクエリ文字列を元に、新しいURLを組み立てます。
echo '●新しく生成されたURL：', PHP_EOL;
$newUrl = $urlElements['scheme'] . '://' . $urlElements['host'] . $urlElements['path'] . '?' . $newQuery;
print_r(urldecode($newUrl));
```
## 正規表現
#### パターン修飾子
- u  
正規表現と対象文字列をUTF-8として処理する
- i  
大文字、小文字を区別しない
- m  
対象文字列が改行を含んでいる場合、^ や $ の正規表現が行頭・行末にも適用されるようにする。
- s  
ドット（.）の正規表現が、改行文字を含むようにする。

#### パターンマッチ（郵便番号/16進数カラーコード/半角英数字/）
```
$matched = [];
echo '●住所から郵便番号を見つける', PHP_EOL;
$address = '123-4567 東京都豊島区...';
preg_match('/^[0-9]{3}\-[0-9]{4}/u', $address, $matched, PREG_OFFSET_CAPTURE);  // 123-4567がマッチする
print_r($matched);

echo '●文字列から16進数表記のカラーコードを見つける', PHP_EOL;
$string = 'darkviolet - 濃いすみれ色 - #9400d3a';
preg_match('/#[0-9a-f]{6}/ui', $string, $matched, PREG_OFFSET_CAPTURE);  // #9400d3がマッチする
print_r($matched);

echo '●半角数字およびハイフンのみかを入力チェックする', PHP_EOL;
$input = '03-９９９９-0000';
if (preg_match('/^[0-9\-]+$/u', $input)) {
    echo '入力に問題ありません。', PHP_EOL;
} else {
    echo '半角数字とハイフンのみで入力してください。', PHP_EOL; // こちらが出力される
}

echo '●半角英数字のみかを入力チェックする', PHP_EOL;
$input = 'HeLLo123';
if (preg_match('/^[a-zA-Z0-9]+$/u', $input)) {
    echo '入力に問題ありません。', PHP_EOL; // こちらが出力される
} else {
    echo '半角英数字のみで入力してください。', PHP_EOL;
}

echo '●半角英数字のみかを入力チェックする(i修飾子)', PHP_EOL;
$input = 'HeLLo123';
if (preg_match('/^[a-z0-9]+$/ui', $input)) {
    echo '入力に問題ありません。', PHP_EOL; // こちらが出力される
} else {
    echo '半角英数字のみで入力してください。', PHP_EOL;
}
```
#### 置換
```
echo '●年月日をドット区切りからハイフン区切りに置換する', PHP_EOL;
$diary = '2019.03.03 今日は雨が降っています.';
$diary = preg_replace('/([0-9]{4})\.([0-9]{2})\.([0-9]{2})/u', '${1}-${2}-${3}', $diary);  // 結果：2019-03-03 今日は雨が降っています.
echo $diary, PHP_EOL;

echo '●[LINK]～[/LINK]の文字列を<a>タグに置換する', PHP_EOL;
$diary = '今日は聖蹟桜ヶ丘を散歩しました。参考：[LINK]http://example.com/seiseki[/LINK]';
$diary = preg_replace('/(\[LINK\])(http:\/\/.+)(\[\/LINK\])/ui', '<a href="${2}">${2}</a>', $diary);  // http://example.com/foodsが<a>タグで囲まれます
echo $diary;
```
#### 分割（文字単位/改行/全角・半角スペース）
```
echo '●文字列を1文字ずつに分解する', PHP_EOL;
$chars = preg_split('//u', 'こんにちは HELLO', -1, PREG_SPLIT_NO_EMPTY);
print_r($chars);

echo '●文字列を改行で分解する', PHP_EOL;
$diary = <<< TEXT
2019年3月3日
今日は雨が降っています。
春が近づいているようです。
TEXT;
$lines = preg_split("/(\r\n|\r|\n)/u", $diary);
print_r($lines);

echo '●文字列を半角または全角スペースで分解する', PHP_EOL;
$words = preg_split("/[ 　]/u", 'あいうえお　かきくけこ さしすせそ');
print_r($words);
```
#### エスケープ
```
// ユーザが入力したフリーワード。A.T.車(オートマ車)の本を探したい。
$freeWord = 'A.T.';

$books = [
    'A.T.車の運転マナー',
    'OAuthによるWebサービス認証入門',
    '睡眠は枕で変わる'
];

echo '●エスケープなしで本を検索する例', PHP_EOL;
foreach ($books as $book) {
    if (preg_match("/{$freeWord}/ui", $book)) {
        echo '・ヒットした本のタイトル：', $book, PHP_EOL;  // 「OAuth」という単語まで引っかかってしまう
        // ドット(.)が任意の位置として認識されてしまう
    }
}

echo '●エスケープして本を検索する例', PHP_EOL;
foreach ($books as $book) {
    if (preg_match('/' . preg_quote($freeWord, '/') . '/ui', $book)) {
        echo '・ヒットした本のタイトル：', $book, PHP_EOL;
    }
}
```
## 数値
#### 最小値/最大値を取得
```
$numbers = [80, 24, 100, 34, 62];
echo '最小値：', min($numbers), PHP_EOL;
echo '最大値：', max($numbers), PHP_EOL;
```
#### 四捨五入、切り上げ、切り捨て
```
$number = 987.654;

echo round($number), PHP_EOL;       // 結果：988
echo round($number,  1), PHP_EOL;   // 結果：987.7
echo round($number,  2), PHP_EOL;   // 結果：987.65
echo round($number,  3), PHP_EOL;   // 結果：987.654
echo round($number,  4), PHP_EOL;   // 結果：987.654
echo round($number, -1), PHP_EOL;   // 結果：990
echo round($number, -2), PHP_EOL;   // 結果：1000

echo ceil($number), PHP_EOL;        // 結果：988

echo floor($number), PHP_EOL;       // 結果：987
```
#### フォーマット化
```
$money = 12345678.12345;
echo number_format($money), PHP_EOL;
echo number_format($money, 3), PHP_EOL;
```
#### BCMatch計算（丸め誤差対応）
```
// 加算する
echo bcadd('0.123', '0.234', 3), PHP_EOL;   // 結果：0.357
echo bcadd('0.123', '0.234', 5), PHP_EOL;   // 結果：0.35700

// べき乗する
echo bcpow('1.2', '3', 3), PHP_EOL;         // 結果：1.728

// 平方根を求める
echo bcsqrt('2', 10), PHP_EOL;              // 結果：1.4142135623

// 2つの値を比較する
echo bccomp('0.12345', '0.12346', 3), PHP_EOL;       // 結果：0(等しい)
echo bccomp('0.12345', '0.12346', 4), PHP_EOL;       // 結果：0(等しい)
echo bccomp('0.12345', '0.12346', 5), PHP_EOL;       // 結果：-1(左オペランドの方が小さい)
```
#### 基数の相互変換（10進数->2進数/2進数->10進数）
```
// 10進数と2進数の相互変換。
echo base_convert('123', 10, 2), PHP_EOL; // 結果：1111011
echo base_convert('1111011', 2, 10), PHP_EOL; // 結果：123

// 16進数から10進数への変換。
// ffc0cbはpinkを表す、16進数カラーコード。
// このカラーコードを2桁ずつ区切って10進数に変換することで、
// 「R：255、G：192、B：203」というRGB値を求めることができる。
echo base_convert('ff', 16, 10), PHP_EOL; // 結果：255
echo base_convert('c0', 16, 10), PHP_EOL; // 結果：192
echo base_convert('cb', 16, 10), PHP_EOL; // 結果：203

// 8進数から2進数への変換。
// 0755はUNIX系OSで使うパーミッション値。
// これを2進数に変換することで、ファイルへのアクセス権限を表すビットパターンを得ることができる。
echo base_convert('0754', 8, 2), PHP_EOL; // 結果：111101100
```
#### バイナリデータを可視化
```
$word = '奥多摩';
echo '●奥多摩を16進数表現に変換します。', PHP_EOL;
echo bin2hex($word), PHP_EOL;

$binary = 'E7AEB1E6A0B9';
echo '●E7AEB1E6A0B9の16進数表現をデコードします。', PHP_EOL;
echo hex2bin($binary), PHP_EOL;
```
## 日付・時間（DateTimeクラス）
#### 日時操作
```
// タイムゾーンをAsia/Tokyoにする。
// デフォルトでは、php.iniのdate.timezone値が使われる。
date_default_timezone_set('Asia/Tokyo');

echo '●現在の日時：', date('Y-m-d H:i:s'), PHP_EOL;

// 日時指定でインスタンスを作る
$dateTime = new DateTime('2019-02-25 17:12:34');
echo $dateTime->format('Y.m.d H:i:s'), PHP_EOL; // 結果：2019.02.25 17:12:34

// インスタンスを変数として保存する必要がなければ、以下のように1ステップでも出力できる
echo (new DateTime('2019-02-25 17:12:34'))->format('Y.m.d H:i:s'), PHP_EOL; // 結果：2019.02.25 17:12:34

// 現在日時を元にしたインスタンスを作る
$dateTime = new DateTime();
echo $dateTime->format('Y.m.d H:i:s'), PHP_EOL; // 結果：<現在の年月日> <現在の時分秒>

// 現在日時の3時間後のインスタンスを作る
$dateTime = new DateTime('+3 hours');
echo $dateTime->format('Y.m.d H:i:s'), PHP_EOL; // 結果：<3時間後の年月日> <3時間後の時分秒>

// 現在日時でインスタンスを作った後、年月日のみを上書き指定する
$dateTime = new DateTime();
$dateTime->setDate(2019, 2, 25);
echo $dateTime->format('Y.m.d H:i:s'), PHP_EOL; // 結果：2019.02.25 <現在の時分秒>

// 現在日時でインスタンスを作った後、年月日と時分秒を上書き指定する
$dateTime = new DateTime();
$dateTime->setDate(2019, 2, 25)->setTime(17, 12, 34);
echo $dateTime->format('Y.m.d H:i:s'), PHP_EOL; // 結果：2019.02.25 17:12:34

// 2019-02-25 17:12:34のインスタンスを作った後、5日+12時間未来に進める
echo (new DateTime('2019-02-25 17:12:34'))
        ->modify('+5 days')
        ->modify('+12 hours')
        ->format('Y.m.d H:i:s'), PHP_EOL; // 結果：2019.03.03 05:12:34

// 2019-02-25 17:12:34のインスタンスを作った後、次の土曜日まで進め、時刻を00:00:00にする
echo (new DateTime('2019-02-25 17:12:34'))
        ->modify('Next Saturday')
        ->setTime(0, 0, 0)
        ->format('Y.m.d H:i:s'), PHP_EOL; // 結果：2019.03.02 00:00:00


// 2019-02-25 17:12:34のインスタンスを作った後、タイムゾーンをUTCに変更して出力する。
// UTCは日本標準時よりも9時間早いため、17:12:34の9時間前の時刻が出力される。
$dateTime = new DateTime('2019-02-25 17:12:34');
$dateTime->setTimeZone(new DateTimeZone('UTC'));
echo $dateTime->format('Y.m.d H:i:s'), PHP_EOL; // 結果：2019.02.25 08:12:34
```
#### 日付フォーマット
```
// タイムゾーンをAsia/Tokyoにする。
// デフォルトでは、php.iniのdate.timezone値が使われる。
date_default_timezone_set('Asia/Tokyo');

$date = new DateTime('2019-02-25 17:12:34');

// フォーマット文字列を指定する例
echo $date->format('Y.m.d H:i:s')           , PHP_EOL; // 結果：2019.02.25 17:12:34
echo $date->format('Y/m/d H:i')             , PHP_EOL; // 結果：2019/02/25 17:12
echo $date->format('Y年m月d日(D) H時i分')   , PHP_EOL; // 結果：2019年02月25日(Mon) 17時12分
echo $date->format('Y.m.t')                 , PHP_EOL; // 結果：2019.02.28(2019年2月の末日)
echo $date->format('U')                     , PHP_EOL; // 結果：1551082354

// DateTimeにあらかじめ用意されたフォーマット定数を使う例
echo $date->format(DateTime::ATOM)          , PHP_EOL; // 結果：2019-02-25T17:12:34+09:00
echo $date->format(DateTime::COOKIE)        , PHP_EOL; // 結果：Monday, 25-Feb-2019 17:12:34 JST

// 協定世界時(UTC)にタイムゾーンを変更する(UTCはAsia/Tokyoよりも9時間早い)
$date->setTimezone(new DateTimeZone('UTC'));
echo $date->format('Y.m.d H:i:s')           , PHP_EOL; // 結果：2019.02.25 08:12:34
```
#### 日付の比較
```
$date1 = new DateTime('2019-02-25 09:23:00');
$date2 = new DateTime('2019-02-25 17:12:34');

var_dump($date1 == $date2); // 結果：false
var_dump($date1 > $date2);  // 結果：false
var_dump($date1 >= $date2); // 結果：false
var_dump($date1 < $date2);  // 結果：true
var_dump($date1 <= $date2); // 結果：true
```
#### 日付の期間に重なりがあるか判定
```
// 期間1は、2019-02-01～2019-02-28
$term1Start = new DateTime('2019-02-01 00:00:00');
$term1End = new DateTime('2019-02-28 23:59:59');

// 期間2は、2019-01-01～2019-02-10
$term2Start = new DateTime('2019-01-01 00:00:00');
$term2End = new DateTime('2019-02-10 23:59:59');

// 期間1(開始) <= 期間2(終了) && 期間1(終了) >= 期間2(開始)
if ($term1Start <= $term2End && $term1End >= $term2Start) {
    echo '2つの期間は重なりあっています', PHP_EOL;              // こちらが出力される
} else {
    echo '2つの期間は重なりあっていません', PHP_EOL;
}
```
#### 日付操作（time/strtotime/date）
```
echo '●現在のUnixタイム：', PHP_EOL;
echo time(), PHP_EOL;

echo '●現在日時を出力：', PHP_EOL;
echo date('Y-m-d H:i:s'), PHP_EOL;

echo '●現在の3日後の日時を出力(time使用)：', PHP_EOL;
echo date('Y-m-d H:i:s', time() + 60 * 60 * 24 * 3), PHP_EOL;

echo '●現在の3日後の日時を出力(strtotime使用)：', PHP_EOL;
echo date('Y-m-d H:i:s', strtotime('+3 days')), PHP_EOL;

echo '●2019-01-10 10:00:00の3時間前を出力：', PHP_EOL;
echo date('Y-m-d H:i:s', strtotime('-3 hours', strtotime('2019-01-10 10:00:00'))), PHP_EOL;

$date1 = '2019-02-25 09:23:00';
$date2 = '2019-02-25 17:12:34';

echo '●date1はdate2よりも未来か？：', PHP_EOL;
var_dump(strtotime($date1) > strtotime($date2));

echo '●date1はdate2よりも過去か？：', PHP_EOL;
var_dump(strtotime($date1) < strtotime($date2));
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```
#### 
```
```