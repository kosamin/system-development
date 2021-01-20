
#### phpinfoパラメータ確認
```
php -r 'phpinfo();'
php -r 'phpinfo();' | grep memory_limit
```



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