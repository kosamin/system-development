## コピー系
### ローカルホストからコンテナにファイルをコピー
docker cp index.php d02cdc8916d0:/var/www/html

### コンテナからローカルホストにファイルをコピー
docker cp d02cdc8916d0:/var/www/html/container.txt .