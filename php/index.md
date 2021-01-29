## phpを使う理由
* 動的なウェブページを作成するため。
* BtoCサービスに適している言語だから
    * 消費者のニーズに応じて、スムーズに変化を対応できるBtoCサービスに向いている。
    * 業務システムを作る場合は、一度作り終わると変更や更新されることが少ない代わりに、より処理速度や処理の正確さが求められるので、javaのような開発に労力（工数）を必要とするが厳密な言語構造を持つプログラム言語が適している。
    * phpは言語構造がそこまで厳密ではないため、手早くwebアプリケーションを仕上げることができ、サービス公開後の機能追加や変更に必要な労力がjavaなどの厳密な言語に比べると、少なく見積もることができる。


## phpの実行方法
* CGI (common Gateway Interface)
    * Apacheなどのwebサーバを経由して実行(拡張モジュールを組み込む必要がある)
* CLI (command Line Interface)
    * OSコマンドとして実行


## FastCGI vs CGI
FastCGIは、CGIよりも処理速度が早い。  
https://wa3.i-3-i.info/word12806.html
* FastCGIは、実行したプログラムをメモリに待機させ、起動と終了の処理を減らすことで処理の高速化と負荷の軽減が見込める



## 浮動小数点の計算（丸め誤差）
phpで浮動小数点の計算結果は近似値で表されるので、実際の計算結果と異なる場合がある。  
対策として、BCMath関数を使用する。（BCMath拡張モジュールをインストールする必要がある）  
※丸め誤差が発生する原因は、10進数を2進数に対応付けが正確にできないため。割り切れない数字はBCDを元に対応する。

```
#普通に計算した場合
floor((0.1 + 0.7) * 10); #表示される値：7
sprintf('%.50f', (0.1 + 0.7) * 10); #実際の値：7.99999999999999911182158029987476766109466552734375

#BCMathモジュールを使用した場合
floor((int)bcmul(bcadd(0.1, 0.7, 2), 10, 2)); #表示される値：8
```

## 浮動小数点の比較
全ての桁を厳密に計算するのは丸め誤差の問題があり、本来等しい扱いが等しくない扱いをされる可能性が発生するので危険。
対策として、「小数部分のある桁までが一致していれば同じ値であるとみなす。」という発想で比較をする。

比較のやり方
* 独自の比較ロジックを作成する(abs関数を使用し絶対値で計算)
* BCMath関数を使用する(bccomp)


## 参照渡し（リファレンス渡し）
参照渡しは、関数の内部と外部で値を共有するために使う。  
※C言語のポインタと異なり、リファレンスの実態はメモリ上のアドレスではなくphpが保持する変数テーブル上にある。
#### ループ処理の参照渡し
**ループ処理が終わった後は、unset関数でリファレンスを必ず削除すること**
```
# 配列をループする参照渡し
$numbers = [3, 5, -1, 2];
foreach ($numbers as &$number) {
    if ($number < 0) {
        $number = 0;
    }
}
unset($number);
print_r($numbers); #Array ( [0] => 3 [1] => 5 [2] => 0 [3] => 2 )



# 連想配列をループする参照渡し(値は上書きできるが、キーは上書きできない)
$colors = [
    'red'       => '赤',
    'blue'      => '青',
    'yellow'    => '黄'
];
foreach ($colors as $key => &$value) {
    $value = 'カラー名：' . $value;
}
unset($value);
print_r($colors); #Array ( [red] => カラー名：赤 [blue] => カラー名：青 [yellow] => カラー名：黄 )



# unset関数でリファレンスを削除しない場合、イレギュラーなエラーや想定外の現象が発生する。
# この処理の例だと、$numbers変数の元の値が書き換わってしまっている。
$numbers = [3, 5, -1, 2];
foreach ($numbers as &$number) {
    if ($number < 0) {
        $number = 0;
    }
}
$number = 100;
print_r($number); #100
print_r($numbers); #Array ( [0] => 3 [1] => 5 [2] => 0 [3] => 100 )
```
## 演算子
#### 宇宙船演算子
「AとBは同じ」「AはBより大きい」「AはBより小さい」の3つの条件を同時に判定できる演算子。  
返り値は0, 1, -1。

* 変数が比較する値と同じ場合、0を返す
* 変数が比較する値よりも大きい場合、1を返す
* 変数が比較する値よりも小さい場合、-1を返す

```
$point = 85;
var_dump($point <=> 85);　#返り値は0
var_dump($point <=> 80);　#返り値は1
var_dump($point <=> 90);　#返り値は-1
```

#### null合体演算子
null判定する比較をコンパクトに記述するための演算子。

```
#値がnullの場合
$greeting = null;
$message = $greeting ?? 'こんにちは';
echo '佐藤さん、'. $message. '!'; #佐藤さん、こんにちは！

#値がnullでない場合
$greeting = 'こんばんは';
$message = $greeting ?? 'こんにちは';
echo '佐藤さん、'. $message. '!'; #佐藤さん、こんばんは！
```

#### 論理演算子の優先順位
&&（論理積）と||（論理和）が含まれる比較の場合、&&が優先で計算される。
||を優先したい場合、括弧で囲む。
```
$a = true;
$b = false;
$c = false;

var_dump($a || $b && $c); #true
var_dump(($a || $b) && $c); #false
```

#### カラ判定
カラ判定の実装パターン。要素数が0の配列や0の扱いがややこしいため、書籍では5を推奨。
1. 条件式 + 論理演算子
2. 比較演算子（厳密ではない）
3. empty
4. isset or is_null
5. **is_null || $a === ''**

```
#カラ判定される値の中身：null, '', 未定義
#カラ判定されない値の中身：[], 0
$value = null;
if (is_null($value) || $value === '') {
    echo '変数はカラです。';
} else {
    echo '変数はカラではありません。';
}
```


## クロージャー（コールバック関数）
関数名を持たず、変数に保存できる特殊な関数。関数の引数として渡される役割でよく使われる。  
変数に値を渡すパターン
```
$addFunction = function($a, $b) {
    return $a + $b;
};
$total = $addFunction(10, 20);
echo $total; #30
```
関数に関数を渡すパターン
```
/* あるユーザの購入履歴を表示する関数 */
function printPurchased(array $items, callable $extraDataFunc): void
{
    echo '<table border="1">';
    echo '<tr>';
    echo '<th>購入日</th>';
    echo '<th>商品名</th>';
    echo '<th>価格</th>';
    echo '<th>その他</th>';
    echo '</tr>';
    foreach ($items as $item) {
        echo '<tr>';
        echo '<td>', $item['date'], '</td>';
        echo '<td>', $item['name'], '</td>';
        echo '<td>', $item['price'], '</td>';
        // 「その他」欄に印字するデータは呼び出し元が自由に設定できます。
        echo '<td>', $extraDataFunc($item), '</td>';
        echo '</tr>';
    }
    echo '</table>';
}

/* メインルーチン */

// あるユーザが購入した商品の配列
$items = [
    [
        'user-id'           => 'tanaka-1234',   // ユーザID
        'date'              => '2018-11-21',    // 購入日
        'name'              => 'ドレスシャツ',  // 商品名
        'price'             => 2160,            // 価格
        'color'             => 'white',         // 色
        'size'              => 'M',             // サイズ
        'item-number'       => 91025,           // 商品No
        'serial'            => 'PLG01219'       // 製造番号
    ],
    [
        'user-id'           => 'tanaka-1234',   // ユーザID
        'date'              => '2018-09-05',    // 購入日
        'name'              => 'キッズパジャマ',// 商品名
        'price'             => 1620,            // 価格
        'color'             => 'red',           // 色
        'size'              => 110,             // サイズ
        'item-number'       => 90081,           // 商品No
        'serial'            => 'ZAQ80124'       // 製造番号
    ]
];

echo '<h3>ユーザのマイページ内の購入履歴</h3>';
printPurchased($items, function ($item) {
    return "色：{$item['color']}　サイズ：{$item['size']}";
});

echo '<h3>販売事業者専用ページ内の購入履歴</h3>';
printPurchased($items, function ($item) {
    return "ユーザID：{$item['user-id']}　商品No：{$item['item-number']}";
});
```
#### useキーワード
useキーワードで変数を指定すると、クロージャーの中でも変数の値を参照できるようになる。
クロージャー外のスコープで定義された変数を見るために使用する。
```
#クロージャーの定義時にuseで指定した値が渡されるため、クロージャー定義後に変数の値が変わってもクロージャーは関係ない。
#クロージャー定義後に変数の値を渡すためには、リファレンス渡しをする。
$greeting = 'Good';
$greetingmaker = function($time) use ($greeting) {
    print $greeting . ' ' . $time. '<br>';
};

$greetingmaker('Morning'); #Good Morning
```


## 外部ファイルの読み込み方法（include, requireの扱い方）
|読み込み命令| 処理内容                                               |
|:-------------------|:------------------------------------------------------|
| require            | 外部ファイルを読み込めなかった場合は処理を中止               |
| require_once       | 外部ファイルを読み込めていなかった場合のみ、require命令を実行  |
| include            | 外部ファイルを読み込めなかった場合も処理を継続               |
| include_once       | 外部ファイルを読み込めていなかった場合のみ、include命令を実行  |
***
**require_onceを使うタイミング:関数が定義されたファイルを読み込むとき**  
* なぜincludeを使わないのか？ => 関数が読み込めない場合は処理を中止すべきだから  
* なぜrequireをつかわないのか？ => 2重に同じ関数を読み込むとエラーになるから。  

**includeを使うタイミング:htmlファイルを読み込むとき**  
* なぜrequireを使わないのか？ => 多くの場合、読み込みに失敗しても処理を中断するほどのことではないから  
* なぜinclude_onceを使わないのか？ => 同じHTMLパーツが1つのページに複数回表示されることがあるから  

#### パスの指定は絶対パスで指定する
phpは最初に実行されたファイルを起点として処理されるため、相対パスで指定すると意図しないエラーが発生することがある。


#### phpだけのプログラムファイルは、タグを閉じない
閉じタグの後に、改行コードや半角スペースがある場合、ウェブページに画像が正常に表示されない場合がある。
phpタグで囲まれていない文字も出力対象のため、画像ファイルの一部として認識され画像が壊れる。

## 静的メソッド
引数の値に応じて一定の戻り値を返す役割を持たせたい場合に使用。
インスタンスの状態に左右されない。
静的メソッドは静的プロパティにのみアクセス可能。
```
class ExcelColumnConverter
{
    // 数値で表された列の番号を元に、アルファベット表記の列名を返す
    public static function calcAlphabetColumnName(int $number): string
    {
        return chr(ord('A') + $number);
    }

    // アルファベット表記の列名を元に、数値で表された列の番号を返す
    public static function calcNumberColumnName(string $alphabet): int
    {
        return ord($alphabet) - ord('A');
    }
}

echo ExcelColumnConverter::calcAlphabetColumnName(3), PHP_EOL; // 出力結果「D」
echo ExcelColumnConverter::calcNumberColumnName('F'); // 出力結果「5」
```

## 静的プロパティ
クラスをインスタンス化しなくてもプロパティにアクセスできるので、２つ以上のプログラム処理で同じプロパティの値を共有できる。
通常のプロパティは、インスタンスごとにプロパティを保持するため、１つのプロパティを共有できない。
```
class ShoppingPoint
{
    public static $point;

    public static function countUpPoint()
    {
        self::$point++;
    }

}

class WeekDayPoint
{
    // 曜日によってポイントを追加する
    public function addWeekDayPoint(string $youbi)
    {
        if($youbi === 'Fri') {
            ShoppingPoint::countUpPoint();
        }
    }
    // 一定以上の金額を購入した場合にポイントを追加する
    public function addPricePoint(int $price)
    {
        if($price >= 1000) {
            ShoppingPoint::countUpPoint();
        }
    }
}

ShoppingPoint::$point = 0;
// 購入するだけで１ポイント追加
ShoppingPoint::countUpPoint();

// 曜日によって１ポイント追加
$weekDayPoint = new WeekDayPoint();
$weekDayPoint->addWeekDayPoint('Fri');

// 購入金額によって１ポイントを追加
$weekDayPoint->addPricePoint(5000);

echo '購入ポイント：'. ShoppingPoint::$point; #3ポイント
```

## 抽象クラス
スーパークラスが持つ抽象メソッドを、サブクラス側に同じ引数と同じ戻り値で必ず実装する必要がある。
```
abstract class Clock
{
    // 現在時刻
    protected $time;

    // 時間を示す
    abstract public function show(): string;

    // 時間をセットする
    public function setTime(int $time): void
    {
        $this->time = $time;
    }

    // 時間を取得する
    public function getTime(): string
    {
        return $this->time;
    }
}

class DigitalClock extends Clock
{
    // Clockクラスの抽象メソッドshow()は、サブクラスで必ず実装する
    public function show(): string
    {
        return date('H:i', $this->time);
    }
}

$currentTime = strtotime('2018-08-22 17:15');
$digitalClock = new DigitalClock();
$digitalClock->setTime($currentTime);
echo $digitalClock->show(); // 結果：17:15
```
#### オーバーライド
スーパークラスが持つメソッドをサブクラス側で上書き、拡張する方法。オーバーライドするとき、メソッドで受け取る引数の数がスーパークラスと異なる場合、余白の引数を設定する。
```
abstract class Clock
{
    // 現在時刻
    protected $time;

    // 時間を示す
    abstract public function show(): string;

    // 時間をセットする
    public function setTime(int $time): void
    {
        $this->time = $time;
    }

    // 時間を取得する
    public function getTime(): string
    {
        return $this->time;
    }
}

class DigitalClock extends Clock
{
    // 文字色
    private $color;

    // 時間をセットする(オーバーライド)
    public function setTime(int $time): void
    {
        if (date('H', $time) >= 6 && date('H', $time) <= 21) {
            // 6時～21時であれば文字色は「白」
            $this->color = 'white';
        } else {
            // それ以外の時間帯であれば文字色は「青」
            $this->color = 'blue';
        }
        $this->time = $time;
    }

    // 文字色を取得する
    public function getColor(): string
    {
        return $this->color;
    }

    // 時間を示す
    public function show(): string
    {
        return date('H:i', $this->time);
    }
}

$currentTime = strtotime('2018-08-22 17:15');
$digitalClock = new DigitalClock();
$digitalClock->setTime($currentTime); // オーバーライドされたsetTime()が呼び出される
echo $digitalClock->getColor();
```

#### 継承時のコンストラクタ
サブクラスにコンストラクタを定義しなかった場合、スーパークラスのコンストラクタを実行する。
サブクラスにコンストラクタを定義した場合、スーパークラスのコンストラクタを上書きし、実行する。（オーバーライド）


## インターフェース
実装クラスがインターフェースに定められたメソッド取り入れることを必須にする役割

※抽象メソッド
スーパークラスの機能を拡張したサブクラスを派生する役割



## 例外処理
#### Exception クラスのプロパティ/メソッド
- $exception->getMessage()  
例外メッセージを取得
- $exception->getTrace()  
例外処理のトレース情報を取得
```
class ClassA
{
    public function methodA(): void
    {
        $classB = new ClassB();
        $classB->methodB();
        echo 'methodA completed.'; // この行は実行されない
    }
}
class ClassB
{
    public function methodB(): void
    {
        throw new Exception('methodBでエラー発生');
        echo 'methodB completed.'; // この行は実行されない
    }
}
try {
    $classA = new ClassA();
    $classA->methodA();
} catch (Exception $exception) {
    echo 'メインルーチンで例外をキャッチ。エラー内容：' , $exception->getMessage(), PHP_EOL;

    file_put_contents("test.log", print_r($exception->getMessage(), true) ."\n",FILE_APPEND);　
    ＃methodBでエラー発生

    file_put_contents("test.log", print_r($exception->getTrace(), true) ."\n",FILE_APPEND);
    #Array (
                [0] => Array
                    (
                        [file] => /var/www/html/index.php
                        [line] => 24
                        [function] => methodA
                        [class] => ClassA
                        [type] => ->
                        [args] => Array
                            (
                            )
                    )
            )
}
```

## シャローコピー/ディープコピー
- シャローコピー
１つのインスタンスのパラメータを参照する状態を持ち、インスタンスのコピーを作成する。
パラメータを変更した場合、コピーされた全てのインスタンスのパラメータが変更される。

- ディープコピー
インスタンス自体を複製し、１つのパラメータを参照する状態を持たずにインスタンスのコピーを作成する。
パラメータを変更しても、他のインスタンスのパラメータには影響がない。
```
// 住所クラス
class Address
{
    // 都道府県
    public $prefecture;

    // 市区町村
    public $city;

    // コンストラクタ
    public function __construct(string $prefecture, string $city)
    {
        $this->prefecture = $prefecture;
        $this->city = $city;
    }
}
// 顧客データクラス
class Customer
{
    // 顧客の氏名を表す、文字列型の普通の変数(cloneされる)
    public $name;

    // 住所を表す、Addressクラス型の変数(cloneされない)
    public $address;

    // コンストラクタ
    public function __construct(string $name, Address $address)
    {
        $this->name = $name;
        $this->address = $address;
    }

    // 氏名を変更する
    public function changeName(string $name): void
    {
        $this->name = $name;
    }

    // 住所を変更する
    public function changeAddress(string $prefecture, string $city): void
    {
        $this->address->prefecture = $prefecture;
        $this->address->city = $city;
    }
    // cloneキーワードが使われた時に自動的にコールされるマジックメソッド
    public function __clone()
    {
        $this->address = clone $this->address;
    }
}
    $customer1 = new Customer('山崎太郎', new Address('東京都', '品川区'));
    $customer2 = clone $customer1;
    $customer2->changeName('伊藤花子');
    $customer2->changeAddress('神奈川県', '横浜市');
    print_r($customer1); // 東京都のはずが、神奈川県になってしまう
    print_r($customer2);
```

## 一定期間データを保持する方法
- Cookie
 - Webブラウザ側でクッキーがブロックされているが、データを保持しなくて良いとき
 - webブラウザを開き直したときにデータを復元したいとき
 - 偽造されても差し支えない情報を扱うとき
- Session
 - Webブラウザ側でクッキーがブロックされているが、データを保持しなくて良いとき
 - webブラウザを開き直したときにデータを復元しなくても良い場合
- データベース
 - Webブラウザ側でクッキーがブロックされていてもデータを保存したい場合
 - 偽造されたら問題がある情報を扱う場合

## Cookieについて
#### Cookieとは
ブラウザを閉じても一定時間、クライアントとサーバの間でデータを維持できる。

#### Cookieの制限
Webブラウザが１ドメインごとに保存できるクッキーの数と、１クッキーあたりのバイト数には上限値がある。  
webブラウザごとに異なる。  
**上限値が少ないブラウザは、クッキー数50、１クッキーあたり4096バイト（４キロバイト）**

#### Cookieを使用するタイミング
- webブラウザを閉じても、再度ブラウザを開いたときにデータを保持していたいとき
- 偽造されても問題ないデータを扱うとき（トークン）

## Sessionについて
#### Sessionとは
ユーザがウェブサイトにアクセスし、画面遷移し、ブラウザを閉じるまでの期間。  
セッション変数は、max-age,expiresが指定されていないセッションクッキー（ブラウザを閉じるまで有効なクッキー）を生成する。

#### セッションを使うタイミング
- ログイン認証後、webブラウザを閉じるまでのログイン状態の維持
- 入力画面でユーザが入力したデータの、入力完了画面までの維持

#### セッション変数が消えるタイミング
- ユーザがwebブラウザを閉じたとき
- ユーザがwebブラウザを開いたまま放置し、一定時間が経過したとき

#### セッションの有効期限
```
# php.ini
session.gc_maxlifetime = 1440
session.gc_probability = 1
session.gc_divisor = 1000


gc_maxlifetime
#セッションの有効期限。秒で指定。1440 = 24分。

gc_probability, gc_divisor
/**
webサーバ上のセッションデータの破棄は、GC（Gabage Collection）で行われる。
この２つのパラメータを指定し、GCの起動確率を設定する。
※GCは、PHPプログラムが実行されたときに一定確率で起動する。

session.gc_probability / session.gc_divisor = GC起動率

有効期限が過ぎたら100%タイムアウトさせたい場合は、session.gc_probability = 1, session.gc_divisor = 1 を設定する。
*/
```
## SQLの分類
- DDL(データ定義言語)  
データベース、テーブルの定義を取り扱うSQL  
※ create, drop, alter
- DML(データ操作言語)  
レコードを取り扱うSQL  
※ select, update, delete, insert
- DCL(データ制御言語)  
ユーザ権限やトランザクションを取り扱うSQL  
※ grant, begin, commit, rollback