## CiecleCiとは
ソフトウェアのビルド、テスト、デプロイを自動化するSass型のCI(継続的インテグレーション)ツール


## 継続的インテグレーションとは
ソースコードを共有リポジトリのmasterブランチに頻繁に統合することを奨励する開発手法


## コンセプト
#### ステップ
ジョブを実行するためのアクション。

#### イメージ
CI環境で利用するDockerコンテナ、VMイメージを指定する。
公式はコンテナ推奨。

下記のいづれかを宣言する必要がある
- docker
- machine
- macos


#### ジョブ
ステップの集まり。


### ワークフロー
ジョブの一覧と実行順序を定義する。
ジョブは実行する選択肢
- 並行実行
- 順次実行
- スケジュール実行




## 設定方法
#### .circleci/config.ymlファイルを作成する

#### Githubに設定を追加する
ciが失敗したときはマージを実行しない設定を追加する。
チェックをつける
- Require status checks to pass before merging
- Require branches to be up to date before merging


sudo chown -R chanmike:chanmike .git/