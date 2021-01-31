## 読みやすいコードにするためのお約束

#### 変数について
- 変数名は中身を表す名前を命名し、検索しやすい状況を整える。
- 数値は定数などに代入し、その数値が何であるか理解できるように整える。
- 完全な名前を命名する（仮名を使わない）

#### 関数について
- 1つの関数つき、1つの処理を実装する。
- 関数名は何をしているのか理解できる名前を命名する。

#### 条件分岐について
- 多重ネスト構造を避けるために、ガード説を使用する
- 条件分岐の式はカプセル化する。
- 否定の条件は避ける
- 分岐後に値を取得する場合、連想配列を使用する。
- 分岐後に関数を実行する場合、Stateパターンを使用する。

```
# 分岐後に値を取得する場合、連想配列を使用する。
$article_types = [
    'news' => 1,
    'entertainment' => 2,
    'recipe' => 3
];

if (!array_key_exists($article->type, $article_types)){
    return null;
}

return $article_types[$article->type];


# Laravel専用
$article_types = [
    'news' => 1,
    'entertainment' => 2,
    'recipe' => 3
];

// array_get(検索対象, 検索文字、存在しない場合のデフォルト値)
return array_get($article_types, $article->type, null);
```
```
# 分岐後に関数を実行する場合、Stateパターンを使用する。
class NewsType {
    public function getInfo() {
        // 情報を取得する処理は省略
        return 1; // 情報
    }
}
class EntertainmentType {
    public function getInfo() {
        // 情報を取得する処理は省略
        return 2; // 情報
    }
}
class RecipeType {
    public function getInfo() {
        // 情報を取得する処理は省略
        return 3; // 情報
    }
}

$article = 'recipe';
// $article->type = 'recipe';

$type_classes = [
    'news' => NewsType::class,
    'entertainment' => EntertainmentType::class,
    'recipe' => RecipeType::class
];

if (!array_key_exists($article, $type_classes))
{
    return null;
}

$type_instance = new $type_classes[$article];
var_dump($type_instance->getInfo());
return $type_instance->getInfo();
```