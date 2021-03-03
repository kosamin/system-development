## マイグレーション

#### テーブル作成
```
php artisan make:migration create_videos_table --create=videos
php artisan make:migration create_team_master_table --create=team_master
php artisan make:migration create_channel_infos_table --create=channel_infos
```
#### カラム変更
```
composer require doctrine/dbal
php artisan make:migration change_videos_table_column  --table=videos
php artisan make:migration add_column_users_table --table=users
php artisan make:migration add_column_login_infos_table --table=users
```
#### カラム追加
```
php artisan make:migration add_character_list_01_table --table=character_list_01
php artisan make:migration add_videos_table --table=videos
php artisan make:migration add_column_videos_table --table=videos
```
#### マイグレーション実行
```
php artisan migrate

# ファイルを指定してマイグレーション実行
php artisan migrate --path=database/migrations/2020_07_18_130454_create_books_table.php

# Seederデータ追加しながら、マイグレーション実行
php artisan migrate --seed

# マイグレーション状態確認
php artisan migrate:status
```

#### ロールバック実行
```
# 前回のマイグレーション処理を対象
php artisan migrate:rollback

# 全マイグレーション処理を対象
php artisan migrate:reset

# 全マイグレーション処理を対象 + 再マイグレーション実行
php artisan migrate:refresh

# 全マイグレーション処理を対象 + 再マイグレーション実行 + Seederデータ追加
php artisan migrate:refresh --seed

# 前回のマイグレーションファイルを対象
php artisan migrate:rollback --step=1
```

#### Seeder作成
```
php artisan make:seeder BooksTableSeeder
php artisan make:seeder TeamMasterTableSeeder

#DatabaseSeeder.phpにクラス名を追加

# seeder実行（全ファイル）
php artisan db:seed

# seeder実行（ファイル指定）
php artisan db:seed --class=UserTableSeeder
```

#### Factory作成
SeederでDBに登録するための初期データを作成する。よくFackerなどダミーデータを作成ツールが使われる。
```
php artisan make:factory PostFactory
php artisan make:factory TalentFactory

# ModelとFactoryを同時に作成
php artisan make:model "Model\Post" --factory

```


## コントローラ作成
--model　はコントローラに名前空間のインポートを追加する。モデルが存在しない場合は、新規作成する。
```
php artisan make:controller HelloController
php artisan make:controller SampleController
php artisan make:controller LiveScheduleController
php artisan make:controller SearchController
php artisan make:controller UserController
php artisan make:controller TopController --model Top
```
#### リソースコントローラ
````
php artisan make:controller PhotoController --resource
php artisan make:controller PhotoController --resource --model=Photo


ルーティング設定
use App\Http\Controllers\PhotoController;
Route::resource('photos', PhotoController::class);

#グループ
Route::resources([
    'photos' => PhotoController::class,
    'posts' => PostController::class,
]);

# 一部のリソースルートのみ使用
Route::resource('photos', PhotoController::class)->only([
    'index', 'show'
]);
Route::resource('photos', PhotoController::class)->except([
    'create', 'store', 'update', 'destroy'
]);

動詞	    URI	                    アクション	  ルート名
GET	        /photos	                index	    photos.index
GET	        /photos/create	        create	    photos.create
POST	    /photos	                store	    photos.store
GET	        /photos/{photo}	        show	    photos.show
GET	        /photos/{photo}/edit	edit	    photos.edit
PUT/PATCH	/photos/{photo}	        update	    photos.update
DELETE	    /photos/{photo}	        destroy	    photos.destroy
````

## ミドルウェア作成
```
php artisan make:middleware Smartphone
```
```
使い方
①app/Http/Kernel.phpにミドルウェアを登録する
②特定の画面のみミドルウェアを有効にしたい場合、ルーティングにミドルウェアの設定をする

特定の画面のみにミドルウェアを有効にしたい場合
protected $routeMiddleware = [
    'admin_auth' => \App\Http\Middleware\AdminAuth::class
];
Route::group(['middleware' => 'admin_auth'], function(){
    Route::get('middleware_test', 'HomeController@middleware_test');
});

全ての画面でミドルウェアを有効にしたい場合
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\AdminAuth::class,
    ],
]
```

## サービスプロバイダの作成
```
php artisan make:provider AgentServiceProvider
```
```
使い方
①サービスプロバイダを作成する
②プロバイダをconfig/app,phpに登録する
'providers' => [
    // Other Service Providers

    App\Providers\ComposerServiceProvider::class,
],
```

## モデル作成
```
php artisan make:model Book
php artisan make:model Video
php artisan make:controller TopController --model Top
```

## バッチ処理作成
```
php artisan make:command GetYoutubeVideos
php artisan make:command PostTweet
php artisan make:command diffPopularRatio

# バッチ実行
php artisan command:
```

## tinker
#### メールテスト
```
Mail::raw('test mail',function($message){$message->to('test@example.com')->subject('test');});
```