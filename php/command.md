## PHP基本操作
#### phpinfoパラメータ確認
```
php -r 'phpinfo();'
php -r 'phpinfo();' | grep memory_limit
```

#### ログ出力コード
```
file_put_contents(__DIR__ . "/../../../storage/logs/test.log", print_r($channelIds, true) ."\n",FILE_APPEND);
```

#### 改行コード
| 改行コード | コード値 |用途 |
| :---:    | :---: | :---: |
| LF       | \n    |Unix系OS全般|
| CR+LF    | \r\n  |Windows系OS|
| CR       | \r    |古いMac OS（9以前|

## 文字列型の操作
#### 文字コード変換
```
echo '●変更前の文字コード：', mb_internal_encoding(), PHP_EOL;
// 文字コードを変更する
mb_internal_encoding('SJIS-Win');
echo '●変更後の文字コード：', mb_internal_encoding(), PHP_EOL;
```

#### 検索
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

#### 切り取り
```
$sentence = '今日は,良い日です';
var_dump(mb_substr($sentence, 4, 3));       // 結果：良い日
var_dump(mb_substr($sentence, -5, 3));      // 結果：良い日
var_dump(mb_substr($sentence, 4));          // 結果：良い日です

var_dump(mb_strstr($sentence, '良', false));    // 結果：良い日です
var_dump(mb_strstr($sentence, '日', false));    // 結果：日は,良い日です
var_dump(mb_strstr($sentence, '日', true));     // 結果：今
```


#### 置換
```
$sentence = '今日は、良い日です';
var_dump(str_replace('今日', 'あした', $sentence)); // 結果：あしたは、良い日です

$sentences = <<< TEXT
いろはにほへと　ちりぬるを
わかよたれそ　つねならむ
TEXT;
var_dump(str_replace(["\r\n", "\r", "\n"], '', $sentences)); // 改行が除去された$sentencesを出力
```
#### 分割
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
#### パディング（穴追加/穴埋め）
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
#### 文字を丸める (mb_strimwidth)
半角は１、全角は２としてカウントする。第４引数が末尾に追加される。
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
#### 文字のエンコード・デコード
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
#### パターン修飾子について
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
## 数値型の操作
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
## 日付・時間（DateTimeクラス）の操作
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
## 配列型の操作
#### 要素の操作（追加/結合/切り取り/削除）
```
$chars = ['a', 'b', 'c'];

echo '●配列に要素を追加する', PHP_EOL;
$chars[] = 'd'; // この書式だと1つずつしか要素を追加できない
array_push($chars, 'e', 'f', 'g');  // array_pushを使うと1回で複数要素を追加できる
print_r($chars);                    // 結果：a,b,c,d,e,f,g

echo '●配列の最初に要素を追加する', PHP_EOL;
array_unshift($chars, 'Z', 'Y', 'X');
print_r($chars);                    // 結果：Z,Y,X,a,b,c,d,e,f,g

echo '●2つの配列を結合する', PHP_EOL;
$chars = array_merge($chars, ['h', 'i', 'j']);
print_r($chars);                    // 結果：Z,Y,X,a,b,c,d,e,f,g,h,i,j

echo '●配列の先頭の要素を取り出す。', PHP_EOL;
$headElement = array_shift($chars);
echo $headElement, PHP_EOL;         // 結果：Z
print_r($chars);                    // 結果：Y,X,a,b,c,d,e,f,g,h,i,j

echo '●配列の末尾の要素を取り出す。', PHP_EOL;
$lastElement = array_pop($chars);
echo $lastElement, PHP_EOL;         // 結果：j
print_r($chars);                    // 結果：Y,X,a,b,c,d,e,f,g,h,i

echo '●配列のキー番号2から5要素分、切り出す', PHP_EOL;
$sliced = array_slice($chars, 2, 5);
print_r($sliced);                   // 結果：a,b,c,d,e
print_r($chars);                    // 結果：Y,X,a,b,c,d,e,f,g,h,i

echo '●配列のキー番号2の要素を削除する(キーは詰まりません)', PHP_EOL;
unset($chars[2]);
print_r($chars);                    // 結果：Y,X,b,c,d,e,f,g,h,i
```
#### キーのみ、値のみを取得
```
$user = [
    'name'          => 'Tanaka',
    'age'           => 20,
    'prefecture'    => 'Osaka',
    'hobbies'       => ['魚釣り', '読書', '作詞']
];

echo '●連想配列のキーのみを取得。', PHP_EOL;
$keys = array_keys($user);
print_r($keys);

echo '●連想配列のキーのうち、値として「Osaka」を持つもののみを取得。', PHP_EOL;
$keys = array_keys($user, 'Osaka', true);
print_r($keys);

echo '●連想配列のキーのうち、値として文字列データ型の「20」を持つもののみを取得。', PHP_EOL;
$keys = array_keys($user, '20', true);
print_r($keys);

echo '●連想配列の値のみを取得。', PHP_EOL;
$values = array_values($user);
print_r($values);
```
#### 全要素に処理を適用する
```
// 有効期限リスト
$expireDates = ['2020-01-03', '2021-12-11', '2019-08-10'];

$newExpireDates = array_map(
    // 有効期限を3年間延長する関数(引数$callback)
    function($date) {
        return (new DateTime($date))->modify('+ 3years')->format('Y-m-d');
    },
    $expireDates
);

echo '●expireDatesの要素(内容はもとのまま)', PHP_EOL;
print_r($expireDates);

echo '●newExpireDatesの要素(内容が書き換わっています)', PHP_EOL;
print_r($newExpireDates);
```
#### 特定のキーだけを取得する
```
$userList = [
    [
        'user_id'       => 100001,
        'user_name'     => 'Tanaka',
        'age'           => 20,
        'prefecture'    => 'Osaka'
    ],
    [
        'user_id'       => 100002,
        'user_name'     => 'Suzuki',
        'age'           => 38,
        'prefecture'    => 'Ehime'
    ],
    [
        'user_id'       => 100003,
        'user_name'     => 'Tsukamoto',
        'age'           => 28,
        'prefecture'    => 'Aichi'
    ]
];

echo '●prefectureキーのみを取得。', PHP_EOL;
$prefectures = array_column($userList, 'prefecture');
print_r($prefectures);

echo '●prefectureキーのみを取得し、戻り値のキーはuser_id値とする。', PHP_EOL;
$prefectures = array_column($userList, 'prefecture', 'user_id');
print_r($prefectures);

echo '●すべてのキーを取得し、戻り値のキーはuser_id値とする。', PHP_EOL;
$prefectures = array_column($userList, null, 'user_id');
print_r($prefectures);
```
#### 区切り文字を追加し、文字列化（一行にまとめる）
```
$hobbies = ['テニス', '散歩', 'バッティング練習'];
$hobbiesAsString = implode('・', $hobbies);
echo '趣味：', $hobbiesAsString, PHP_EOL;
```
#### 並び替え（sort）
```
$prices = [200, 3100, '150', 2500, 90];

echo '●配列値で昇順ソートする。', PHP_EOL;
sort($prices);
print_r($prices);

echo '●配列値で降順ソートする。', PHP_EOL;
rsort($prices);
print_r($prices);

echo '●配列値を文字列として昇順ソートする。', PHP_EOL;
sort($prices, SORT_STRING);
print_r($prices);

$files = ['image1.png', 'image105.png', 'image2.png', 'image3.png', 'image44.png'];
echo '●配列値で自然順ソートする。', PHP_EOL;
sort($files, SORT_NATURAL);
print_r($files);

$items = [
    100001      =>      '掃除機',
    109913      =>      'エアコン',
    100409      =>      'PC',
    100004      =>      'テレビ'
];

echo '●連想配列キーの昇順でソートする。', PHP_EOL;
ksort($items);
print_r($items);

echo '●連想配列キーの降順でソートする。', PHP_EOL;
krsort($items);
print_r($items);
```
#### 独自のルールで並び替える(usort)
```
$users = [
    [
        'name'          => 'Hanako SUZUKI', // 氏名
        'num-of-posts'  => 15               // 投稿数
    ],
    [
        'name'          => 'Ichiro YANAGI',
        'num-of-posts'  => 92
    ],
    [
        'name'          => 'Akira HANAYAMA',
        'num-of-posts'  => 15
    ],
    [
        'name'          => 'Ayami YOSHIKAWA',
        'num-of-posts'  => 8
    ]
];
usort($users, function($a, $b) {
    if ($a['num-of-posts'] === $b['num-of-posts']) {
        return $a['name'] <=> $b['name'];
    }
    return ($a['num-of-posts'] <=> $b['num-of-posts']) * -1;
});
print_r($users);
```
#### 要素数を調べる
```
$chars = ['a', 'b', 'c'];
echo count($chars), PHP_EOL;    // 結果：3

$chars = [
    'a' => 'あ',
    'i' => 'い',
    'u' => 'う'
];
echo count($chars), PHP_EOL;    // 結果：3

$user = [
    'name'          => 'Tanaka',
    'age'           => 20,
    'prefecture'    => 'Osaka',
    'hobbies'       => ['魚釣り', '読書', '作詞']
];
echo count($user), PHP_EOL;    // 結果：4
echo count($user, COUNT_RECURSIVE), PHP_EOL;    // 結果：7
```
#### 範囲指定で配列作成
```
$list = range(1, 15);           // 結果：1～15の配列
print_r($list);

$list = range(0, 100, 10);      // 結果：0,10,20,30...100の配列
print_r($list);

$list = range('a', 'e');        // 結果：a～eの配列
print_r($list);

$list = range('A', 'E');        // 結果：A～Eの配列
print_r($list);
```
## ファイル/ディレクトリ操作
#### 基本操作（ファイル名/ディレクトリ名/拡張子）
```
echo '●相対パスでファイルを指定した場合', PHP_EOL;
$filePath = './files/dir1/dir2/file.txt';
echo 'ファイル名：', basename($filePath), PHP_EOL;
echo 'ファイル名(拡張子なし)：', basename($filePath, '.txt'), PHP_EOL;
echo 'ディレクトリ名：', dirname($filePath), PHP_EOL;
echo 'ディレクトリ名(2階層上)：', dirname($filePath, 2), PHP_EOL;
echo '絶対パス：', realpath($filePath), PHP_EOL;
echo '拡張子：', pathinfo($filePath, PATHINFO_EXTENSION), PHP_EOL;
echo 'ファイル名：', pathinfo($filePath, PATHINFO_FILENAME), PHP_EOL;

echo '●絶対パスでファイルを指定した場合', PHP_EOL;
$filePath = '/Users/kentooguri/localSystems/php/honkaku/chapter04/file1/files/dir1/dir2/file.txt';
echo 'ファイル名：', basename($filePath), PHP_EOL;
echo 'ディレクトリ名：', dirname($filePath), PHP_EOL;
echo '絶対パス：', realpath($filePath), PHP_EOL;
echo '拡張子：', pathinfo($filePath, PATHINFO_EXTENSION), PHP_EOL;
```
#### 存在チェック
```
// 存在するファイルに対するfile_exists
var_dump(file_exists('files/note.txt'));        // 結果：true

// 存在しないファイルに対するfile_exists
var_dump(file_exists('files/ghost.txt'));       // 結果：false

// 存在するディレクトリに対するfile_exists
var_dump(file_exists('files/dir1'));            // 結果：true

// 存在しないディレクトリに対するfile_exists
var_dump(file_exists('files/ghost-dir'));       // 結果：false
```
#### コピー/リネーム/削除
```
echo '●flower.pngをコピーしてflower-copy1.pngを作ります。', PHP_EOL;
copy('./files/flower.png', './files/flower-copy1.png');

echo '●flower.pngをコピーしてflower-copy2.pngを作ります。', PHP_EOL;
copy('./files/flower.png', './files/flower-copy2.png');

echo '●flower.pngをコピーしてflower-copy3.pngを作ります。', PHP_EOL;
copy('./files/flower.png', './files/flower-copy3.png');

echo '●flower-copy1.pngをflower-rename1.pngにリネームします。', PHP_EOL;
rename('./files/flower-copy1.png', './files/flower-rename1.png');

echo '●flower-copy2.pngを削除します。', PHP_EOL;
unlink('./files/flower-copy2.png');
```
#### ファイルの読み込み
```
echo '●file関数でファイルを読み込みます。', PHP_EOL;
$lines = file('files/note.txt'); //テキストデータの読み込み可能
foreach ($lines as $line) {
    echo trim($line), PHP_EOL;
}

echo '●file_get_contents関数でファイルを読み込みます。', PHP_EOL;
$contents = file_get_contents('files/note.txt'); //テキスト&バイナリデータを読み込み可能
echo $contents, PHP_EOL;

echo '●readfile関数でファイルを読み込みます。', PHP_EOL;
readfile('files/note.txt'); //テキスト&バイナリデータを読み込み可能
```
#### ファイルの書き込み
```
file_put_contents("test.log","### START oguri " . date("Y/m/d H:i:s ") . __FILE__ . "(line ".__LINE__.")↓↓↓\n",FILE_APPEND);
file_put_contents("test.log", print_r($result, true) ."\n",FILE_APPEND);
file_put_contents("test.log","### E N D oguri " . date("Y/m/d H:i:s ") . __FILE__ . "(line ".__LINE__.")↑↑↑\n",FILE_APPEND);

    $log = <<< LOG
■2019-01-01 12:34:56 [ERROR] 画像ファイルが見つかりません。
■2019-01-02 12:34:56 [INFO] ユーザがログインしました。

LOG;
    file_put_contents('files/log.txt', $log);

    $log = <<< LOG
■2019-01-03 12:34:56 [INFO] フリーワード検索されました。
■2019-01-04 12:34:56 [INFO] PDFファイルがアップロードされました。

LOG;
    file_put_contents('files/log.txt', $log, FILE_APPEND);

    echo '●log.txtの内容は以下の通りです。', PHP_EOL;
    readfile('files/log.txt');
```
#### ファイル情報取得（作成日時/最終更新日時/最終アクセス日時）
```
define('LOCK_FILE', 'files/lock');

if (file_exists(LOCK_FILE)) {
    unlink(LOCK_FILE);
}

echo '●現在日時：', date('Y-m-d H:i:s'),  PHP_EOL;

echo '●ファイル「lock」を作成します。', PHP_EOL;
touch(LOCK_FILE);

echo '●ファイル「lock」の更新日時を3時間後に、アクセス日時を5時間後に変更します。', PHP_EOL;
touch(LOCK_FILE, time() + 60 * 60 * 3, time() + 60 * 60 * 5);

echo '作成日時：', date('Y-m-d H:i:s', filectime(LOCK_FILE)), PHP_EOL;
echo '最終更新日時：', date('Y-m-d H:i:s', filemtime(LOCK_FILE)), PHP_EOL;
echo '最終アクセス日時：', date('Y-m-d H:i:s', fileatime(LOCK_FILE)), PHP_EOL;
```
#### MIMEタイプ取得
```
$finfo = finfo_open(FILEINFO_MIME_TYPE);

// PNG画像ファイルのMIMEタイプを取得する
echo finfo_file($finfo, 'files/flower.png'), PHP_EOL;   // 結果：image/png

// 本当はテキスト形式なのに、拡張子がpdfに偽装されているファイルのMIMEタイプを取得する
echo finfo_file($finfo, 'files/dummy.pdf'), PHP_EOL;    // 結果：text/plain

finfo_close($finfo);
```
#### ディレクトリを生成
```
echo '●new-dir1ディレクトリを作成します。', PHP_EOL;
mkdir('files/new-dir1');

echo '●new-dir2ディレクトリを作成します。', PHP_EOL;
mkdir('files/new-dir2');

echo '●new-dir1ディレクトリを削除します。', PHP_EOL;
rmdir('files/new-dir1');
```
#### パターンで検索
```
$files = glob('files/user-images/user1234-*.{jpg,jpeg,gif,png}', GLOB_BRACE);
print_r($files);
```
#### ファイルの読み込み
```
echo '●file関数でファイルを読み込みます。', PHP_EOL; //テキストデータの読み込み可能
$lines = file('files/note.txt');
foreach ($lines as $line) {
    echo trim($line), PHP_EOL;
}

echo '●file_get_contents関数でファイルを読み込みます。', PHP_EOL; //テキスト&バイナリデータを読み込み可能
$contents = file_get_contents('files/note.txt');
echo $contents, PHP_EOL;

echo '●readfile関数でファイルを読み込みます。', PHP_EOL; //テキスト&バイナリデータを読み込み可能
readfile('files/note.txt');
```
## ファイル操作用のクラス（SplFileObject/DirectoryIterator）
#### ファイル読み込み（一行ずつ）
```
// sample.txtを読み込みます
$file = new SplFileObject('files/sample.txt');
$file->setFlags(SplFileObject::READ_AHEAD | SplFileObject::SKIP_EMPTY | SplFileObject::DROP_NEW_LINE);

// sample.txtを1行ずつループ処理します。
$lineNumber = 0;
foreach ($file as $line) {
    $lineNumber++;
    echo "{$lineNumber}行目：『{$line}』", PHP_EOL;
}
$file = null;



#クラス未使用の場合（内部関数を使う場合）
$handle = fopen('files/sample.txt', 'r');
while ($line = fgets($handle)) {
    $line = trim($line);
    if ($line === '') {
        continue;
    }
    echo $line, PHP_EOL;
}
fclose($handle);
```
#### CSVを読み込む
```
setlocale(LC_ALL, 'ja_JP.UTF-8');

require_once dirname(__FILE__) . '/functions.php';

// sample.csvの文字コードをUTF-8に変換し、sample-utf.csvとして保存します。
sjis2utf('files/sample.csv', 'files/temp/sample-utf.csv');

// sample-utf.csvを読み込みます。
$csv = new SplFileObject('files/temp/sample-utf.csv');
$csv->setFlags(SplFileObject::READ_CSV);

// sample-utf.csvを1行ずつループ処理します。
// fieldsPerLineはCSVをフィールド単位に区切った配列です。
foreach ($csv as $fieldsPerLine) {
    print_r($fieldsPerLine);
}
$csv = null;
```
```
#functions.php

/**
 * ファイルの文字コードを変換し、別ファイルとして保存する関数。
 * @param string $fromFile 変換元ファイルパス。存在するファイルを指定すること。
 * @param string $toFile 変換先ファイルパス。既に存在する場合は上書き保存される事に注意。
 * @param string $fromEncoding 変換元文字コード
 * @param string $toEncoding 変換先文字コード
 */
function convertEncoding(string $fromFile, string $toFile, string $fromEncoding, string $toEncoding): void
{
    file_put_contents(
        $toFile,
        mb_convert_encoding(
            file_get_contents($fromFile),
            $toEncoding,
            $fromEncoding
        )
    );
}

/**
 * ファイルの文字コードをUTF-8からSJIS-winに変換し、別ファイルとして保存する関数。
 * @param string $fromFile 変換元ファイルパス。存在するファイルを指定すること。
 * @param string $toFile 変換先ファイルパス。既に存在する場合は上書き保存される事に注意。
 */
function utf2sjis(string $fromFile, string $toFile): void
{
    convertEncoding($fromFile, $toFile, 'UTF-8', 'SJIS-win');
}

/**
 * ファイルの文字コードをSJIS-winからUTF-8に変換し、別ファイルとして保存する関数。
 * @param string $fromFile 変換元ファイルパス。存在するファイルを指定すること。
 * @param string $toFile 変換先ファイルパス。既に存在する場合は上書き保存される事に注意。
 */
function sjis2utf(string $fromFile, string $toFile): void
{
    convertEncoding($fromFile, $toFile, 'SJIS-win', 'UTF-8');
}

```
#### ファイルに書き込み（ファイル/CSV）
```
// テキストファイルへの書き込み
$file = new SplFileObject('files/generated.txt', 'w');
$text = <<< TEXT
いろはにほへと　ちりぬるを
わかよたれそ　つねならむ
うゐのおくやま　けふこえて
あさきゆめみし　ゑひもせす
TEXT;
$bytes = $file->fwrite($text);
echo '●generated.txtに', $bytes, 'バイトを書き込みました。', PHP_EOL;


// CSVファイルへの書き込み
$csv = new SplFileObject('files/temp/generated-utf.csv', 'w');
$items = [
    ['商品名', '価格'],
    ['掃除機', '15,000'],
    ['エアコン', '60,000'],
    ['アイロン高性能', '20,000'],
    ["1行目\n\"2行目\"\n3行目", '30,000'],
];
foreach ($items as $item) {
    $csv->fputcsv($item);
}
// generated-utf.csvの文字コードをShift-JISに変換し、generated.csvとして保存します。
utf2sjis('files/temp/generated-utf.csv', 'files/generated.csv');
echo '●generated.csvに書き込みました。', PHP_EOL;
$csv = null;
```
#### ディレクト内を調べる
```
$dir = new DirectoryIterator('./files');

foreach ($dir as $file) {
    if ($file->isDot()) {
        continue;
    }

    $type = '';
    if ($file->isFile()) {
        $type = 'ファイル';
    } elseif ($file->isDir()) {
        $type = 'ディレクトリ';
    }
    if ($file->isLink()) {
        $type .= '(シンボリックリンク)';
    }
    echo $type, ':', $file->getFileName(), PHP_EOL;
}
$dir = null;

// ファイル:flower.png
// ファイル:generated.csv
// ファイル:generated.txt
// ファイル:sample.csv
// ファイル:sample.txt
// ディレクトリ:temp
```
## 妥当性のチェック
#### データ型
```
// 論理型のチェック
var_dump(is_bool('true'));          // 結果：false
var_dump(is_bool(true));            // 結果：true
var_dump(is_bool(false));           // 結果：true

// 数値型のチェック
var_dump(is_int(123));              // 結果：true
var_dump(is_int('123'));            // 結果：false
var_dump(is_int(123.456));          // 結果：false

// 数値または数字であるかのチェック
var_dump(is_numeric(123));          // 結果：true
var_dump(is_numeric('123'));        // 結果：true
var_dump(is_numeric('123.456'));    // 結果：true
var_dump(is_numeric('string'));     // 結果：false

// オブジェクト型のチェック
var_dump(is_object('object'));      // 結果：false
var_dump(is_object(new stdClass()));// 結果：true

// nullチェック
var_dump(is_null(null));            // 結果：true
var_dump(is_null(''));              // 結果：false
var_dump(is_null(0));               // 結果：false
```
#### 日付
```
var_dump(checkdate(12, 31, 2018));  // 結果：true
var_dump(checkdate(8, 99, 2018));   // 結果：false
var_dump(checkdate(2, 29, 2020));   // 結果：true
var_dump(checkdate(2, 29, 2019));   // 結果：false
```
#### 空(カラ)
```
var_dump(empty(0));     // 結果：true
var_dump(empty('0'));   // 結果：true
var_dump(empty('a'));   // 結果：false
var_dump(empty(0.1));   // 結果：false
var_dump(empty(0.0));   // 結果：true
var_dump(empty(''));    // 結果：true
var_dump(empty(null));  // 結果：true
var_dump(empty([]));    // 結果：true
var_dump(empty(true));  // 結果：false
var_dump(empty(false)); // 結果：true
```
#### 存在チェック（変数/配列キー/連想配列キー）
```
// スカラー変数に対するチェック
$a = 'a';
var_dump(isset($a));            // 結果：true
var_dump(isset($b));            // 結果：false

$z = null;
var_dump(isset($z));            // 結果：false

// 配列のキーに対するチェック
$chars = ['a', 'i', 'u'];
var_dump(isset($chars));      // 結果：true
var_dump(isset($chars[0]));   // 結果：true
var_dump(isset($chars[1]));   // 結果：true
var_dump(isset($chars[2]));   // 結果：true
var_dump(isset($chars[3]));   // 結果：false
var_dump(array_key_exists(0, $chars));   // 結果：true
var_dump(array_key_exists(3, $chars));   // 結果：false

// 連想配列のキーに対するチェック
$chars = [
    'a' => 'あ',
    'i' => 'い',
    'u' => 'う',
    'e' => null,
    'o' => 'お'
];
var_dump(isset($chars['a']));   // 結果：true
var_dump(isset($chars['e']));   // 結果：false
var_dump(isset($chars[0]));     // 結果：false
var_dump(array_key_exists('a', $chars));   // 結果：true
var_dump(array_key_exists('e', $chars));   // 結果：true(値がnullでもtrue)
```
#### 存在チェック（配列の値/連想配列の値）
```
$chars = ['a', 'i', 'u', 'e', 'o'];
var_dump(in_array('u', $chars, true));      // 結果：true
var_dump(in_array('U', $chars, true));      // 結果：false(大文字小文字は区別)
var_dump(in_array('n', $chars, true));      // 結果：false
var_dump(in_array(0, $chars, true));        // 結果：false

$chars = [
    'a' => 'あ',
    'i' => 'い',
    'u' => 'う',
    'e' => 'え',
    'o' => 'お'
];
var_dump(in_array('a', $chars, true));      // 結果：false(キーは見ない)
var_dump(in_array('n', $chars, true));      // 結果：false(キーは見ない)
var_dump(in_array('あ', $chars, true));     // 結果：true
var_dump(in_array('ん', $chars, true));     // 結果：false
```

#### インスタンスの型チェック
```
abstract class AbstractFile {}

interface Drawable {}

class Image extends AbstractFile implements Drawable {}

class Pdf extends AbstractFile {}

class Nothing {}

$pdf = new Pdf();
var_dump($pdf instanceof Pdf);                  // 結果：true
var_dump($pdf instanceof Image);                // 結果：false
var_dump($pdf instanceof AbstractFile);         // 結果：true
var_dump($pdf instanceof Drawable);             // 結果：false

$image = new Image();
var_dump($image instanceof Pdf);                // 結果：false
var_dump($image instanceof Image);              // 結果：true
var_dump($image instanceof AbstractFile);       // 結果：true
var_dump($image instanceof Drawable);           // 結果：true

$nothing = new Nothing();
var_dump($nothing instanceof Pdf);              // 結果：false
var_dump($nothing instanceof Image);            // 結果：false
var_dump($nothing instanceof AbstractFile);     // 結果：false
var_dump($nothing instanceof Drawable);         // 結果：false
var_dump($nothing instanceof GhostClass);       // 結果：false
```
#### 存在チェック（クラス/関数/メソッド）
```
class SomeClass
{
    public static function doStatic() {}

    public function doPublic() {}

    private function doPrivate() {}
}

$someClass = new SomeClass();

var_dump(class_exists('SomeClass', false));             // 結果：true
var_dump(class_exists('GhostClass', false));            // 結果：false

require_once dirname(__FILE__) . '/classes/ExternalClass1.php';
var_dump(class_exists('ExternalClass1', false));            // 結果：true
var_dump(class_exists('ExternalClass2', false));            // 結果：false(require_onceしていないためfalse)

var_dump(method_exists($someClass, 'doPublic'));        // 結果：true
var_dump(method_exists($someClass, 'doPrivate'));       // 結果：true
var_dump(method_exists('SomeClass', 'doPublic'));       // 結果：true
var_dump(method_exists('SomeClass', 'doStatic'));       // 結果：true
var_dump(method_exists($someClass, 'doNothing'));       // 結果：false

var_dump(is_callable([$someClass, 'doPublic']));        // 結果：true
var_dump(is_callable([$someClass, 'doPrivate']));       // 結果：false(privateメソッドは呼べない)
var_dump(is_callable([$someClass, 'doStatic']));        // 結果：true
var_dump(is_callable([$someClass, 'doPrivate'], true)); // 結果：true

function calc() {}
var_dump(is_callable('calc'));                          // 結果：true
var_dump(function_exists('calc'));                      // 結果：true
var_dump(function_exists('nothing'));                   // 結果：false
```
## 乱数
#### ランダムな数値
```
// 単純にランダムな数値を生成する例
echo 'ランダムな番号：', random_int(1, 99999), PHP_EOL;

// ランダムな顧客コードを生成する例
$code = 'customer-' . str_pad(strval(random_int(1, 99999)), 5, '0', STR_PAD_LEFT);
echo '顧客コードは：', $code, PHP_EOL;

// ランダムな配列要素を選択する例
$colors = ['red', 'blue', 'green', 'yellow', 'black'];
echo 'ラッキーカラーは：', $colors[random_int(0,4)], PHP_EOL;

// ランダムなURLを生成する例
function generateRandom(int $minLength, int $maxLength)
{
    $length = random_int($minLength, $maxLength);
    $chars = array_merge(range('a', 'z'), range('A', 'Z'), range('0', '9'));
    print_r($chars);
    $random = '';
    for ($i = 0; $i < $length; $i++) {
        print_r(count($chars));
        $random .= $chars[random_int(0, count($chars) - 1)];
    }
    return $random;
}
echo 'このURLをクリックして、ユーザ登録を完了してください：http://example.com/user?key=', generateRandom(16, 32), PHP_EOL;
```
#### ランダムなバイト数
```
// 5バイトのランダム値を生成する。
// 各バイトは0x00～0xffの範囲となる。
$bytes = random_bytes(5);
echo bin2hex($bytes), PHP_EOL;   // 結果例：03648d5fcb(10文字になる)

$bytes = openssl_random_pseudo_bytes(5);
echo bin2hex($bytes), PHP_EOL;   // 結果例：d08dafbdd3(10文字になる)
```
## ハッシュ化と暗号化
#### パスワードをハッシュ化
```
$hashedPassword = password_hash('abcde1234(^^)v', PASSWORD_DEFAULT, ['cost' => 13]);
echo 'ハッシュ化されたパスワード：', $hashedPassword, PHP_EOL;

$input = 'abcde1234(^^)v';
if (password_verify($input, $hashedPassword)) {
    echo 'パスワードは一致しています。', PHP_EOL;       // このブロックに入る
} else {
    echo 'パスワードは一致していません。', PHP_EOL;
}

$input = 'xyz9876(^^)v';
if (password_verify($input, $hashedPassword)) {
    echo 'パスワードは一致しています。', PHP_EOL;
} else {
    echo 'パスワードは一致していません。', PHP_EOL;     // このブロックに入る
}
```
#### ファイルをハッシュ化
```
echo 'flower1.pngのmd5ハッシュ値', hash_file('md5', 'files/flower1.png'), PHP_EOL;
echo 'flower2.pngのmd5ハッシュ値', hash_file('md5', 'files/flower2.png'), PHP_EOL;
echo 'flower3.pngのmd5ハッシュ値', hash_file('md5', 'files/flower3.png'), PHP_EOL;

echo 'flower1.pngのsha256ハッシュ値', hash_file('sha256', 'files/flower1.png'), PHP_EOL;
echo 'flower2.pngのsha256ハッシュ値', hash_file('sha256', 'files/flower2.png'), PHP_EOL;
echo 'flower3.pngのsha256ハッシュ値', hash_file('sha256', 'files/flower3.png'), PHP_EOL;
```
#### データを暗号化/復号
```
require_once dirname(__FILE__) . '/Crypter.php';

/**
    * cipher.keyに保存されているキーは暗号化と復号のためのパスワードの役目を果たす文字列です。
    * キーが外部に漏れないようにしてください。
    *
    * このプログラムではAES-256-CBCの暗号化アルゴリズムを使いますので、
    * キーの長さは256ビット(=32バイト)である必要があります。
    *
    * cipher.keyに保存されている値は、他の環境に流用しないようにしてください。
    * 他の環境で使うときは、以下のプログラムを実行することで、cipher.keyの内容を書き換えてください。
    *
    *      $bytes = openssl_random_pseudo_bytes(32);
    *      file_put_contents('cipher.key', base64_encode($bytes));
    */

// cipher.keyの内容をBASE64デコードすると、256ビット(=32バイト)のキーになります。
$key = base64_decode(file_get_contents('cipher.key'));

// 暗号化したい文字列
$address = '東京都渋谷区渋谷99-99-99 テストマンション901号室';

// 暗号化を行います。encryptメソッドの戻り値の$encryptedと$ivは、両方とも復号に必要です。
// データベースには、$encryptedと$ivの両方を、カラムを分けて保存してください。
$crypter = new Crypter($key);
list($encrypted, $iv) = $crypter->encrypt($address, true);
echo '●暗号化された文字列：', PHP_EOL;
echo $encrypted, PHP_EOL;
echo '●暗号化された文字列のIV：', PHP_EOL;
echo $iv, PHP_EOL;

// 復号を行います。
$crypter = new Crypter($key);
$decrypted = $crypter->decrypt($encrypted, $iv, true);
echo '●復号された文字列：', PHP_EOL;
echo $decrypted, PHP_EOL;

echo '●IVの長さを取得：', PHP_EOL;
$ivLength = openssl_cipher_iv_length('AES-256-CBC');
echo $ivLength, PHP_EOL;

echo '●使用可能な暗号化アルゴリズム：', PHP_EOL;
print_r(openssl_get_cipher_methods(false));
```
```
## Crypter.php

/**
 * 暗号化・復号を行うクラス
 */
class Crypter
{
    /**
     * デフォルトで使う暗号化アルゴリズム
     */
    private const DEFUALT_METHOD = 'AES-256-CBC';

    /**
     * 暗号化・復号のためのキー
     */
    private $key;

    /**
     * 暗号化アルゴリズム
     */
    private $method;

    /**
     * コンストラクタ
     */
    public function __construct(string $key, string $method = null)
    {
        $this->key = $key;
        if (is_null($method)) {
            $this->method = self::DEFUALT_METHOD;
        } else {
            $this->method = $method;
        }
    }

    /**
     * 文字列を暗号化する
     * @param string $value 暗号化したい文字列
     * @param bool $isBase64 戻り値をbase64エンコードして返す場合はtrueを指定
     * @return array 0番目の要素として暗号化済の文字列、1番目の要素としてIVを持つ配列
     */
    public function encrypt(string $value, bool $isBase64 = true): array
    {
        $ivLength = openssl_cipher_iv_length($this->method);
        $iv = openssl_random_pseudo_bytes($ivLength);
        $encrypted = openssl_encrypt($value, $this->method, $this->key, OPENSSL_RAW_DATA, $iv);
        if ($isBase64) {
            return [base64_encode($encrypted), base64_encode($iv)];
        } else {
            return [$encrypted, $iv];
        }
    }

    /**
     * 文字列を復号する。
     * @param string $value 暗号化済の文字列
     * @param string $iv IV文字列
     * @param bool $isBase64 引数$value、$ivがbase64エンコードされている場合はtrueを指定
     * @return 復号された文字列
     */
    public function decrypt(string $value, string $iv, bool $isBase64 = true): string
    {
        if ($isBase64) {
            $iv = base64_decode($iv);
            $value = base64_decode($value);
        }
        $decrypted = openssl_decrypt($value, $this->method, $this->key, OPENSSL_RAW_DATA, $iv);
        return $decrypted;
    }
}
```
## コマンドラインからプログラム実行
#### コマンドから実行時、引数が指定されたかを判定
```
// 値の有無
// mode::任意
// from:, to:必須
// verbose: 不要

$options = getopt('m::f:t:v', ['mode::', 'from:', 'to:', 'verbose']);
var_dump($options);
```
#### コマンドから実行時のオプションを設定
```
/**
 * 契約期限から1年以上経過した顧客をデータベースから削除するバッチ処理。
 * 本バッチ処理には、以下のコマンドライン引数を指定することができる。
 *
 * ●-v または --verbose
 *   この引数指定時、開発者向けのデバッグ情報を出力しながら実行します。
 *
 * ●-d または --dry-run
 *   空回り(Dry Run)させるための引数。
 *   この引数指定時、データベース削除処理を実行せず、削除対象の顧客IDを出力するのみにとどめます。
 *
 * ●-c または --customer-id
 *   一部の顧客IDのみを対象に処理するための引数。この引数が無い場合は全顧客を対象に処理します。
 *   以下が使用例です。
 *
 *   --customer-id=1234                 -> 顧客IDが1234のみを対象に処理します
 *   --customer-id=1234,2345,3456       -> 3つの顧客IDを対象に処理します
 *   --customer-id=1001-1500            -> 顧客IDが1001～1500の500顧客のみを対象に処理します
 */
class ExpiredCustomersDeleteBatch
{

    /**
     * コマンドライン引数を格納した連想配列
     */
    private $options;

    /**
     * 詳細出力オプションの有無
     */
    private $isVerbose;

    /**
     * 空回り(Dry-Run)オプションの有無
     */
    private $isDryRun;

    /**
     * 対象顧客IDの配列
     */
    private $customerIds;

    /**
     * コンストラクタ
     */
    public function __construct($options)
    {
        $this->options = $options;
        $this->parseOptions();
    }

    /**
     * バッチ処理を実行する。
     */
    public function exec()
    {
        echo '■顧客削除バッチ処理を実行します。', PHP_EOL;
        echo '●詳細情報の出力：', $this->isVerbose ? 'true' : 'false', PHP_EOL;
        echo '●空回り(Dry-Run)：', $this->isDryRun ? 'true' : 'false', PHP_EOL;
        echo '●対象顧客ID：', print_r($this->customerIds, true), PHP_EOL;

        // ここに、顧客削除処理が入ります。
    }

    /**
     * コマンドライン引数に$keysで指定されたいずれかの引数が指定されているかを調べる。
     * @param array $keys 調べたいコマンドライン引数名。配列で複数指定可。
     * @return 指定した引数が存在すればtrue。しなければfalse。
     */
    private function isOptionExists(array $keys): bool
    {
        foreach ($keys as $key) {
            if (array_key_exists($key, $this->options)) {
                return true;
            }
        }
        return false;
    }

    /**
     * コマンドライン引数の-cまたは--customer-idを解析する。
     */
    private function parseCustomerIds(): ?array
    {
        $customerIds = '';
        if (array_key_exists('c', $this->options)) {
            $customerIds = $this->options['c'];
        } elseif (array_key_exists('customer-id', $this->options)) {
            $customerIds = $this->options['customer-id'];
        }
        if (!trim($customerIds)) {
            return null;
        }
        if (preg_match('/,/u', $customerIds)) {
            return explode(',', $customerIds);
        }
        if (preg_match('/\-/u', $customerIds)) {
            list($start, $end) = explode('-', $customerIds);
            return range($start, $end);
        }
        return [$customerIds];
    }

    /**
     * コマンドライン引数を解析する。
     */
    private function parseOptions(): void
    {
        $this->isVerbose = $this->isOptionExists(['v', 'verbose']);
        $this->isDryRun = $this->isOptionExists(['d', 'dry-run']);
        $this->customerIds = $this->parseCustomerIds();
    }
}


// メインルーチン
$options = getopt('vc:d', ['verbose', 'customer-id:', 'dry-run']);
var_dump($options);
$batch = new ExpiredCustomersDeleteBatch($options);
$batch->exec();

```
#### メール送信
初期設定
- sendmail_pathを設定
- stmpサーバ接続先情報を設定

```

    mb_language('Japanese');
    mb_internal_encoding('UTF-8');

    /**
     * mb_send_mailの第4引数にセットする日本語のエンコード
     */
    function encodeHeader($value)
    {
        return mb_encode_mimeheader
            (
                mb_convert_encoding($value, 'ISO-2022-JP', 'UTF-8'),
                'ISO-2022-JP',
                'B'
            );
    }

    /**
     * 送信元のメールアドレス。ご自身のものに書き換えてください。
     */
    $from = '[FROM-ADDRESS-HERE]';

    /**
     * 送信先のメールアドレス。送信して問題の無いアドレスに書き換えてください。
     */
    $to = '[TO-ADDRESS-HERE]';

    /**
     * メールの表題
     */
    $subject = 'テストメール';

    /**
     * メールの本文
     */
    $body = <<< BODY
このメールはPHPからテスト送信したものです。(1行目)
このメールはPHPからテスト送信したものです。(2行目)
このメールはPHPからテスト送信したものです。(3行目)
BODY;

    /**
     * メールの送信元(日本語表記)
     */
    $sender = encodeHeader('システム管理者');

    // メールのヘッダ行を生成する。
    $header = <<< HEADER
From: {$sender} <{$from}>
Reply-To: {$from}
HEADER;

    // メール送信する。
    $isMailSent = mb_send_mail($to, $subject, $body, $header);

    echo $isMailSent ? 'メールを送信しました。' : 'メールは送信できませんでした。';
    echo PHP_EOL;

```
#### バックトレース情報を取得
```
class SomeClass
{
    public function doSomething1()
    {
        $this->doSomething2('Hello');
    }

    public function doSomething2(string $arg)
    {
        $this->doSomething3($arg);
    }

    public function doSomething3(string $arg)
    {
        print_r(debug_backtrace());
    }
}

// メインルーチン
$someClass = new SomeClass();
$someClass->doSomething1();
```
#### メモリの使用量を取得
```
// 何も処理していないので、メモリ使用量は小さいはず。
echo '現在のメモリ使用量(1回目)：', memory_get_usage(), PHP_EOL;

// $listに10000要素入れる。これで少しメモリ使用量が増える。
$list = [];
for ($i = 0; $i < 10000; $i++) {
    $list[] = $i;
}
echo '現在のメモリ使用量(2回目)：', memory_get_usage(), PHP_EOL;

// $listを空にする。これでメモリ使用量が元に戻るはず。
$list = null;
echo '現在のメモリ使用量(3回目)：', memory_get_usage(), PHP_EOL;

// $listに、前回より少なめの2000要素を入れる。
// 2回目の出力よりは小さくなるはず。
$list = [];
for ($i = 0; $i < 2000; $i++) {
    $list[] = $i;
}
echo '現在のメモリ使用量(4回目)：', memory_get_usage(), PHP_EOL;

// 最大メモリ使用量は、$listに10000要素入っていた2回目の出力くらいになるはず。
echo 'メモリの最大使用量：', memory_get_peak_usage(), PHP_EOL;
```
## Cookie
#### Cookieをセット
```
setcookie("name1", 'value1', time() + 60 * 60, '/', '', false, true);
```
#### 更新
```
setcookie("name1", 'value1', time() + 60 * 60, '/', '', false, true);
setcookie("name1", 'value2', time() + 60 * 20, '/', '', false, true);
```
#### 削除
```
setcookie("name1", 'value1', time() + 60 * 60, '/', '', false, true);
setcookie("name1", '', time() - 60 * 60, '/', '', false, true);
```
## Session
#### sessionセット
```
function validate()
{
    return ($_POST['email'] !== '' && $_POST['message'] !== '');
}
session_start();
// 条件①
if (isset($_POST['operation']) && $_POST['operation'] === 'inquiry') {
    // 条件①-A
    if (validate() === false) {
        $message = 'メールアドレス・お問い合わせ内容のいずれも必須入力です。';
        $data = [
            'email' => $_POST['email'],
            'message' => $_POST['message']
        ];
    // 条件①-B
    } else {
        $_SESSION['data'] = [
            'email' => $_POST['email'],
            'message' => $_POST['message']
        ];
        header('Location: confirm.php');
        return;
    }
// 条件② 確認画面から戻るリンクを押された場合
} elseif (isset($_SESSION['data'])) {
    $data = [
        'email' => $_SESSION['data']['email'],
        'message' => $_SESSION['data']['message']
    ];
}
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>セッションの利用 - honkaku</title>
</head>
<body>
    <h2>お問い合わせ入力</h2>
    <?php if (isset($message)) : ?>
        <p style="color:red"><?=$message?></p>
    <?php endif; ?>
    <form name="inquiry-form" action="" method="POST">
        ●メールアドレス：<br>
        <input type="text" name="email" value="<?=isset($data['email']) ? $data['email'] : ''?>"><br>
        ●お問い合わせ内容：<br>
        <textarea name="message" cols="30" rows="4"><?=isset($data['message']) ? $data['message'] : ''?></textarea><br>
        <button type="submit" name="operation" value="inquiry">送信</button>
    </form>
</body>
</html>

```
#### session削除
```
function deleteSession()
{
    if (ini_get('session.use_cookies')) {
        $params = session_get_cookie_params();
        setcookie(session_name(), '', time() - 42000,
            $params['path'], $params['domain'],
            $params['secure'], $params['httponly']
        );
    }
}
session_start();
?>
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>セッションの利用 - honkaku</title>
</head>
<body>
    <h2>お問い合わせ完了</h2>
    <p>お問い合わせありがとうございました。デバッグ情報：</p>
    <pre><?php print_r($_SESSION); ?></pre>
    <?php $_SESSION = []; ?>
    <?php deleteSession(); ?>
    <?php session_destroy(); ?>
</body>
</html>
```
## ファイルアップロード
```
/**
 * ファイル名を元に拡張子を返す関数
 */
function getExtension(string $file): string
{
    return pathinfo($file, PATHINFO_EXTENSION);
}

/**
 * アップロードファイルの妥当性をチェックする関数
 */
function validate(): array
{
    // PHPによるエラーを確認する
    if ($_FILES['image1']['error'] !== UPLOAD_ERR_OK) {
        return [false, 'アップロードエラーを検出しました。'];
    }

    // ファイル名から拡張子をチェックする
    if (!in_array(getExtension($_FILES['image1']['name']), ['jpg', 'jpeg', 'png', 'gif'])) {
        return [false, '画像ファイルのみアップロード可能です。'];
    }

    // ファイルの中身を見てMIMEタイプをチェックする
    $finfo = finfo_open(FILEINFO_MIME_TYPE);
    $mimeType = finfo_file($finfo, $_FILES['image1']['tmp_name']);
    finfo_close($finfo);
    if (!in_array($mimeType, ['image/jpeg', 'image/png', 'image/gif'])) {
        return [false, '不正な画像ファイル形式です。'];
    }

    // ファイルサイズをチェックする
    if (filesize($_FILES['image1']['tmp_name']) > 1024 * 1024 * 2) {
        return [false, 'ファイルサイズは2Mbまでとしてください。'];
    }

    return [true, null];
}

/**
 * アップロード後の保存ファイル名を生成して返す関数
 */
function generateDestinationPath(): string
{
    return 'uploaded/' . date('Ymd-His-') . rand(10000, 99999) . '.' .
        getExtension($_FILES['image1']['name']);
}

/**
 * HTMLエンティティに変換する関数
 */
function escape(string $value): string
{
    return htmlspecialchars($value, ENT_QUOTES | ENT_HTML5, 'UTF-8');
}

/*
 * メインルーチン
 */
list($result, $message) = validate();
if ($result !== true) {
    echo '[Error]', $message;
    return;
}

$destinationPath = generateDestinationPath();
$moved = move_uploaded_file($_FILES['image1']['tmp_name'], $destinationPath);
if ($moved !== true) {
    echo 'アップロード処理中にエラーが発生しました。';
    return;
}
?>

<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>ファイルアップロード - honkaku</title>
</head>
<body>
    <p>アップロードに成功しました。保存された画像は以下です。</p>
    <img src="<?=$destinationPath?>" style="width:300px"><br>
    (保存ファイル名：<?=escape($destinationPath)?>)<br>
    (元のファイル名：<?=escape($_FILES['image1']['name'])?>)
</body>
</html>
```
## 入力フォーム
```
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <title>入力フォーム - honkaku</title>
</head>
<body>
    <h2>お問い合わせ画面</h2>
    <form name="inquiry" action="thankyou.php" method="post">
        <table border="1">
            ### テキストボックス
            <tr>
                <th>お名前</th>
                <td><input type="text" name="simei" value=""></td>
            </tr>
            <tr>
                <th>メールアドレス</th>
                <td><input type="text" name="email" value=""></td>
            </tr>
            ### セレクトボックス
            <tr>
                <th>地域</th>
                <td>
                  <select name="area">
                    <option value=""></option>
                    <option value="北海道">北海道</option>
                    <option value="東北">東北</option>
                    <option value="関東">関東</option>
                    <option value="中部">中部</option>
                    <option value="近畿">近畿</option>
                    <option value="中国">中国</option>
                    <option value="四国">四国</option>
                    <option value="九州">九州</option>
                  </select>
                </td>
            </tr>
            ### チェックボックス
            <tr>
                <th>お問い合わせ種別</th>
                <td>
                    <input type="checkbox" name="type[]" value="ユーザ登録について"> ユーザ登録について<br>
                    <input type="checkbox" name="type[]" value="商品に関するお問い合わせ"> 商品に関するお問い合わせ<br>
                    <input type="checkbox" name="type[]" value="返品について"> 返品について<br>
                </td>
            </tr>
            ### テキストエリア
            <tr>
                <th>お問い合わせ内容</th>
                <td><textarea name="inquiry" rows="3" cols="50"></textarea></td>
            </tr>
            ### ラジオボタン
            <tr>
                <th>お得な情報をEメールで受け取りますか</th>
                <td>
                    <input type="radio" name="news_type" value="受け取ります"> 受け取ります<br>
                    <input type="radio" name="news_type" value="受け取りません"> 受け取りません<br>
                </td>
            </tr>
        </table>
        <button type="submit" name="operation" value="send">送信する</button>
    </form>
</body>
</html>
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