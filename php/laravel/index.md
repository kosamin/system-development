# DB操作
## 登録処理
DBに登録するやり方は3つある。以下の理由から、登録処理はsaveのやり方を使っていく。
1. saveメソッド
2. insertメソッド(DBクラス)
3. insertメソッド(ORM)

insertメソッドのデメリット
- insertはイベント(dispatchesEvents)の自動実行ができない。
- created_at, updated_atを明示的に指定しないとNULLになる。

```
# saveメソッドのやり方
$item = new \App\Models\Talent;
$item->name = $talent_name;
$item->name_kana = $talent_info['name_kana'];
$item->channel_id = $talent_info['channel_id'];
$item->save();

# DBクラスを読み込むやり方
DB::table('items')->insert([
    'name' => '名前'
]);

# Eloquentモデルのやり方
Talent::insert([
    'name' => '名前'
]);
```

## 取得処理
DBからデータを取得するやり方は２種類。可読性が高いORMを使っていく。
1. ORM
2. DBクラス

```
# ORMを使うやり方
use App\Item;
class HomeController extends Controller
{
    public function index() {
        $items = Item::get();
    }
}


# DBクラスを使うやり方
$items = \DB::table('items')->get();    // 全てのデータが取得できる
```



# 各種設定
## 環境変数
環境変数に空白を含めたい場合は、ダブルクォーテーションで囲む。
```
APP_NAME="My Application"
```
## キャッシュ
全設定ファイルを1つのファイルにまとめることで、Laravelが設定ファイルの読み込む速度が早くなる。
```
設定ファイルのキャッシュを作成する。
php artisan config:cache


config:cacheコマンドを実行する場合、.envファイルは読み込まずenv関数の呼び出しがnullになる。
対策
\App\Config\内の設定ファイルに値を定義する。
```

## メンテナンスモード
メンテナンスモード中は、アプリのリクエストを全て指定したビューに遷移させる。(503など)  
また、キューされたジョブは実行されない。
```
メンテナンスモード開始
php artisan down

メンテナンスモードを抜ける
php artisan up

```