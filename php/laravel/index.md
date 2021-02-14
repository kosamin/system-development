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
```
全設定ファイルを1つのファイルにまとめることで、Laravelが設定ファイルの読み込む速度が早くなる。
php artisan config:cache

キャッシュファイル内のルート処理の呼び出しをひとまとめにするため、ルート登録のパフォーマンスを向上させる。
php artisan route:cache

全てのBladeビューを事前にコンパイルし、ビューをレスポンスする際のパフォーマンスを向上させる。
php artisan view:cache

config:cacheコマンドを実行する場合、.envファイルは読み込まずenv関数の呼び出しがnullになる。
対策
\App\Config\内の設定ファイルに値を定義する。
```
## キャッシュクリア
```
ルートキャッシュのクリア
新しいルートを追加する場合は、再度キャッシュを作り直す必要がある。
php artisan route:clear

ビューキャッシュのクリア
php artisan view:clear

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
# ルーティング
## 基本
```
Route::get('/user', [UserController::class, 'index']);


Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```
## リダイレクト
```
Route::redirect('/here', '/there');
Route::redirect('/here', '/there', 301);
Route::permanentRedirect('/here', '/there');
```
## ビュールート
```
Route::view('/welcome', 'welcome');
Route::view('/welcome', 'welcome', ['name' => 'Taylor']);
```
## CSRF
webルートファイルに定義したPOST,PUT,PATCH,DELETEに送るフォームは、CSRFトークンを含める必要がある。  
CSRFトークンを含めていない灰はリクエストが拒否される、
```
<form method="POST" action="/profile">
    @csrf
    ...
</form>
```
## ルートパラメータ
```
Route::get('/user/{id}', function ($id) {
    return 'User '.$id;
});

Route::get('/posts/{post}/comments/{comment}', function ($postId, $commentId) {
    //
});

Route::get('/user/{id}', function (Request $request, $id) {
    return 'User '.$id;
});

Route::get('/user/{name?}', function ($name = 'John') {
    return $name;
});
```
## 名前付きルート
```
Route::get('/user/profile', function () {
    //
})->name('profile');
```


# ミドルウェア
HTTPリクエストに対して特定の処理を実行する。
ミドルウェアの実行タイミングは、処理の実装方法による。
```
php artisan make:middleware CheckAge
```
```
リクエストが処理される前に実行
class BeforeMiddleware
{
    public function handle($request, Closure $next)
    {
        // アクションを実行…

        return $next($request);
    }
}

リクエストが処理された後に実行
class AfterMiddleware
{
    public function handle($request, Closure $next)
    {
        $response = $next($request);

        // アクションを実行…

        return $response;
    }
}
```
## 追加方法
#### 全てのHTTPリクエストにミドルウェアを適用したいとき
```
app/Http/Kernel.phpクラスの$middlewareプロパティへ追加
    protected $middleware = [
        // \App\Http\Middleware\TrustHosts::class,
        \App\Http\Middleware\TrustProxies::class,
        \Fruitcake\Cors\HandleCors::class,
        \App\Http\Middleware\PreventRequestsDuringMaintenance::class,
        \Illuminate\Foundation\Http\Middleware\ValidatePostSize::class,
        \App\Http\Middleware\TrimStrings::class,
        \Illuminate\Foundation\Http\Middleware\ConvertEmptyStringsToNull::class,
    ];
```
#### 特定のルートに対してミドルウェアを追加したいとき
```
①app/Http/Kernel.phpファイルでミドルウェアの短縮キーを$routeMiddlewareに登録
    protected $routeMiddleware = [
        'auth' => \App\Http\Middleware\Authenticate::class,
        'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
        'cache.headers' => \Illuminate\Http\Middleware\SetCacheHeaders::class,
        'can' => \Illuminate\Auth\Middleware\Authorize::class,
        'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
        'password.confirm' => \Illuminate\Auth\Middleware\RequirePassword::class,
        'signed' => \Illuminate\Routing\Middleware\ValidateSignature::class,
        'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
        'verified' => \Illuminate\Auth\Middleware\EnsureEmailIsVerified::class,
    ];

②ルートにミドルウェアを設定する
# 単数
Route::get('admin/profile', function () {
    //
})->middleware('auth');

# 複数
Route::get('/', function () {
    //
})->middleware('first', 'second');

# ミドルウェアグループ＆一部除外（withoutMiddleware）
※withoutMiddlewareはルートミドルウェアのみに適用する。
Route::middleware([CheckAge::class])->group(function () {
    Route::get('/', function () {
        //
    });

    Route::get('admin/profile', function () {
        //
    })->withoutMiddleware([CheckAge::class]);
});
```
## ミドルウェアグループ
複数のミドルウェアをまとめて適用できる。  
routes/web.phpはwebミドルウェアグループが自動で適用される。
```
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        // \Illuminate\Session\Middleware\AuthenticateSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],

    'api' => [
        'throttle:api',
        \Illuminate\Routing\Middleware\SubstituteBindings::class,
    ],
];

Route::get('/', function () {
    //
})->middleware('web');

Route::group(['middleware' => ['web']], function () {
    //
});

Route::middleware(['web', 'subscribed'])->group(function () {
    //
});
```
## ミドルウェアの優先度
app/Http/Kernel.phpファイルの$middlewarePriorityプロパティでミドルウェアの優先度を指定できる。
```
protected $middlewarePriority = [
    \Illuminate\Session\Middleware\StartSession::class,
    \Illuminate\View\Middleware\ShareErrorsFromSession::class,
    \Illuminate\Contracts\Auth\Middleware\AuthenticatesRequests::class,
    \Illuminate\Routing\Middleware\ThrottleRequests::class,
    \Illuminate\Session\Middleware\AuthenticateSession::class,
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
    \Illuminate\Auth\Middleware\Authorize::class,
];
```
## HTTPレスポンス後に実行するミドルウェア
ミドルウェアにterminateメソッドを定義しWebサーバがFastCGIを使用している場合、レスポンスがブラウザへ送られた後に自動的に呼び出される。
```
class StartSession
{
    public function handle($request, Closure $next)
    {
        return $next($request);
    }

    public function terminate($request, $response)
    {
        // セッションデーターの保存…
    }
}
```

# リクエスト
## リクエスト情報の取得
#### リクエストURLのパスを取得
```
$uri = $request->path(); # http://example.com/foo/bar => foo/bar
```
#### リクエストURLのパス判定/名前付きルート名の判定
```
if ($request->is('admin/*')) {
    //
}

if ($request->routeIs('admin.*')) {
    //
}
```
#### リクエストURLの取得
```
クエリ文字列を含まない(?以降の文字列は取得しない)
$url = $request->url();

クエリ文字列を含む(?以降の文字列を取得する)
$urlWithQueryString = $request->fullUrl();
```
#### ヘッダ情報を取得
```
$value = $request->header('X-Header-Name');

#第二引数はデフォルト値
$value = $request->header('X-Header-Name', 'default');
```
#### IPアドレスを取得
```
$ipAddress = $request->ip();
```
#### コンテントタイプ判定
```
サーバ側で受付可能なコンテンツタイプを取得
$contentTypes = $request->getAcceptableContentTypes();

コンテンツタイプ判定
if ($request->accepts(['text/html', 'application/json'])) {
    // ...
}

コンテンツタイプの優先度を指定
$preferred = $request->prefers(['text/html', 'application/json']);

JSONのコンテンツタイプ判定
if ($request->expectsJson()) {
    // ...
}
```
## リクエスト情報取得（入力データ）
#### 全ての入力データを取得
```
$input = $request->all();
```
#### 特定の入力データを取得(input)
リクエストペイロード全体から値を取得する。  
第二引数はデフォルト値
```
$name = $request->input('name');
$name = $request->input('name', 'Sally');

全ての入力値を連想配列で取得
$input = $request->input();

配列入力の値を取得
$name = $request->input('products.0.name');
$names = $request->input('products.*.name');
```
#### 特定の入力データを取得(query)
クエリ文字列から値を取得する  
第二引数はデフォルト値
```
$name = $request->query('name');
$name = $request->query('name', 'Helen');


全てのクエリ文字列を連想配列で取得
$query = $request->query();
```
#### 特定の入力データを取得(Requestインスタンス)
```
$name = $request->name;
```
#### 入力データの一部を取得(only, except)
```
指定したキーのみを取得
$input = $request->only('username', 'password');

指定したキー以外を取得
$input = $request->except('credit_card');
```
#### 入力値判定(has)
指定したパラメータが存在する場合にTRUE
```
if ($request->has('name')) {
    //
}

全てのパラメータが存在する場合にTRUE
if ($request->has(['name', 'email'])) {
    //
}
```
#### 入力値判定（hasAny）
指定したパラメータのいづれかが存在する場合にTRUE
```
if ($request->hasAny(['name', 'email'])) {
    return 'this is hasAny';
}
```
#### 入力値パラメータのから判定(filled)
指定したリクエストパラメータが存在する場合にTRUE  
から文字列はTRUEになる。
```
if ($request->filled('name')) {
    return 'this is filled';
}
```
#### 入力値パラメータのキー判定(missing)
入力値パラメータのキーが存在しないときTRUE
```
if ($request->missing('name')) {
    return 'this is missing';
}
```
## Session
#### セッションに値を登録
```
$request->flash();

一部の値をセッションに登録
$request->flashOnly(['username', 'email']);
$request->flashExcept('password');
```
#### リクエスト前に保持した入力値を取得（old）
```
$username = $request->old('username');

oldヘルパ
<input type="text" name="username" value="{{ old('username') }}">
```
## Cookie
#### リクエストからクッキーを取得
````
$value = $request->cookie('name');
````
## ファイル
#### アップロードされたファイルを取得
````
$file = $request->file('photo');
$file = $request->photo;
````
#### リクエストにファイルが存在するか判定(hasFile)
````
if ($request->hasFile('photo')) {
    //
}
````
#### 正常なファイルのアップロードか判定（isValid）
````
if ($request->file('photo')->isValid()) {
    //
}
````
#### ファイルのパスと拡張子を取得
````
$path = $request->photo->path();

$extension = $request->photo->extension();
````
#### アップロードされたファイルの保存(store)
````
ファイル名は一意のIDが割り当てられる、
$path = $request->photo->store('images');
$path = $request->photo->store('images', 's3');

ファイル名を自動的に生成したくない場合
$path = $request->photo->storeAs('images', 'filename.jpg');
$path = $request->photo->storeAs('images', 'filename.jpg', 's3');
````
## プロキシ設定
#### 特定のプロキシを設定
````
class TrustProxies extends Middleware
{
    /**
     * このアプリケーションで信頼できるプロキシ
     *
     * @var string|array
     */
    protected $proxies = [
        '192.168.1.1',
        '192.168.1.2',
    ];

    /**
     * プロキシを検出するために使用すべきヘッダ
     *
     * @var int
     */
    protected $headers = Request::HEADER_X_FORWARDED_ALL;
}
````
# レスポンス
## リダイレクト
#### 名前付きルートのリダイレクト
````
return redirect()->route('login');
return redirect()->route('profile', ['id' => 1]);
````
#### コントローラアクションへのリダイレクト
````
return redirect()->action([UserController::class, 'index']);
return redirect()->action(
    [UserController::class, 'profile'],
    ['id' => 1]
);
````
#### 外部ドメインへのリダイレクト
URLエンコード、バリデーション処理は実行されない。
````
return redirect()->away('https://www.google.com');
````
#### リダイレクト時にセッションに値を保持する
````
Route::post('/user/profile', function () {
    return redirect('dashboard')->with('status', 'Profile updated!');
});

@if (session('status'))
    <div class="alert alert-success">
        {{ session('status') }}
    </div>
@endif
````
#### JSONレスポンス
````
return response()->json([
    'name' => 'Abigail',
    'state' => 'CA',
]);
````
# ビュー
#### 基本的な操作
````
<html>
    <body>
        <h1>Hello, {{ $name }}</h1>
    </body>
</html>

Route::get('/', function () {
    return view('greeting', ['name' => 'James']);
});

階層構造にも対応
resources/views/admin/profile.blade.php
return view('admin.profile', $data);
````
#### viewの存在チェック
````
use Illuminate\Support\Facades\View;

if (View::exists('emails.customer')) {
    //
}
````
#### 優先順位を指定
配列内で最初に存在するビューを作成する。
````
return view()->first(['custom.admin', 'admin'], $data);


use Illuminate\Support\Facades\View;
return View::first(['custom.admin', 'admin'], $data);
````
#### viewへデータを渡す
````
全データをview関数に追加
return view('greetings', ['name' => 'Victoria']);

withメソッドにデータを個別で追加
return view('greeting')->with('name', 'Victoria');
````
#### 全てのviewでデータ共有
サービスプロバイダが実行するタイミングでshareメソッドを呼び出す。
````
namespace App\Providers;

use Illuminate\Support\Facades\View;

class AppServiceProvider extends ServiceProvider
{
    /**
     * 全アプリケーションサービスの登録
     *
     * @return void
     */
    public function register()
    {
        //
    }

    /**
     * 全アプリケーションサービスの初期起動
     *
     * @return void
     */
    public function boot()
    {
        View::share('key', 'value');
    }
}
````
#### viewコンポーザ
viewがレンダリングされる前に実行する。
````
````
#### viewクリエーター
アプリケーションがインスタンス化されたタイミングで実行する。
````
````
#### viewの最適化
リクエストを受け取ったタイミングで、コンパイル済みのビューとビューの更新時間を確認する。  
過去にコンパイルしたファイルよりも現在のviewが更新されている場合、Laravelの更新されたviewをコンパイルする。
```
php artisan view:cache
php artisan view:clear
````
# Bladeのテンプレート
#### データの表示
{{ }}エコー文は、XSS攻撃を防ぐために、PHPのhtmlspecialchars関数を通して自動的に送信する。
PHP関数の結果をエコーできる。
````
Route::get('/pizza', function () {
    return view('pizza', ['name' => 'マルゲリータ']);
});
Route::get('/pizza', function () {
    return view('pizza')->with('name', 'ペスカトーレ');
});

<h1>{{ $name }}</h1>
<p>The current UNIX timestamp is {{ date('Y-m-d') }}.</p>
````
#### JSONのレンダリング
@jsonディレクティブは、PHPのjson_encode関数と同じ引数を渡せる。  
デフォルトの@jsonディレクティブはJSON_HEX_TAG、JSON_HEX_APOS、JSON_HEX_AMP、JSON_HEX_QUOTフラグを使用する。
````
Route::get('/pizza', function () {
    return view('pizza', ['name' => 'マルゲリータ', 'array' =>  ['apple', 'banana', 'peach']]);
});
<script>
    var app = <?php echo json_encode($array); ?>;
    console.log(app);
</script>

@jsonディレクティブ
var app = @json($array);
var app = @json($array, JSON_PRETTY_PRINT);
````
#### エスケープしない
````
Hello, {!! $name !!}.
````
#### IF文
````
@if (count($array) === 1)
    I have one array!
@elseif (count($array) > 1)
    I have multiple arrays!
@else
    I don't have any arrays!
@endif
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````
#### 
````
````