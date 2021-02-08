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
php artisan make:migration add_column_users_table --table=users
php artisan make:migration add_column_login_infos_table --table=users
```
#### カラム追加
```
php artisan make:migration add_character_list_01_table --table=character_list_01
php artisan make:migration add_videos_table --table=videos
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
```
php artisan make:controller HelloController
php artisan make:controller TopController --model Top
```

## モデル作成
```
php artisan make:model Book
php artisan make:model TeamMaster
php artisan make:controller TopController --model Top
```

## バッチ処理作成
```
php artisan make:command GetYoutubeVideos
php artisan make:command diffPopularRatio

# バッチ実行
php artisan command:
```