# php

- [php](#php)
  - [php-fpm](#php-fpm)
  - [構文](#構文)
    - [基本](#基本)
      - [組込関数](#組込関数)
      - [ファイル読込](#ファイル読込)
    - [フォーム](#フォーム)
  - [開発環境](#開発環境)
  - [php.ini](#phpini)

バカーーー！！！

## [php-fpm](https://hackers-high.com/linux/php-fpm-config/)

PHPをFastCGIインターフェイス経由でサーバーに提供するためのプロセスマネージャ

- pm.max_children: プロセスマネージャで起動する最大子プロセス数を設定。プロセスマネージャが、子プロセスを起動する際に、この設定値を超えないようにします。同時リクエストの処理能力を向上させることができますが、同時に大量の子プロセスを起動すると、メモリ使用量が増加するため、サーバーのパフォーマンスに影響を与える可能性がある
- pm.start_servers: プロセスマネージャが起動するときに最初に起動する子プロセスの数を設定
- pm.min_spare_servers: プロセスマネージャが管理する子プロセスの最小数を設定。空きプロセスがこの値より少なくなった場合、プロセスマネージャは新しい子プロセスを起動します
- pm.max_spare_servers: プロセスマネージャが管理する子プロセスの最大数を設定。空きプロセスがこの値を超える場合、プロセスマネージャは子プロセスを終了させます

簡単にまとめると、pm.max_childrenは最大同時実行数を設定し、pm.max_spare_serversはアイドル状態で保持するプロセス数を設定するための設定です。pm.max_childrenが大きすぎる場合、システムの負荷が増加し、pm.max_spare_serversが小さすぎる場合、Webサーバのリクエスト処理が遅延する可能性があります。適切な設定を行うことで、Webサーバのパフォーマンスを最適化することができます。

## 構文

### 基本

```html
<?php
echo('PHPです(シングルクオテーション)')
echo('<br>')
echo("PHPです(ダブルクオテーション)")
?>
```

- コメントアウト

```html
// １行コメントアウト

/*
複数コメントアウト1 
複数コメントアウト2 
*/
```

- 変数

```html
<?php
$test1 = 456;
$test2 = 789;
$test3 = $test1 . $test2
echo $test3;
```

- 配列

```html
// 配列基本
$array = [];
$array = [1,2,3];
echo '<pre>';
echo $array[0];
echo '</pre>';

// 配列複数行
$array2 = [
  [赤,青,黄],
  [白,黒,灰]
];

// 連想配列
$array_member = [
  'name' => '田中’,
  'height' => 170,
  'weight' -> 65,
]
?>
```

- 演算子

```php
// 四則演算子
$test1 = 9
$test2 = 1
$test3 = $test1 + $test2
$test3 = $test1 - $test2
$test3 = $test1 * $test2
$test3 = $test1 / $test2
$test3 = $test1 % $test2 // 余り値

// 比較演算子

// if文
if ($test1 == 9) {
  処理したい内容
}

// == 一致 stringの'10'もintの10も同じ扱い
// === 型も一致

// if else文
if ($test1 == 9) {
  処理したい内容
}else if ($test1 >= 1){
  処理したい内容
}else{
  処理したい内容
}

// ~ではない
if ($test1 != 9) {
  処理したい内容
}
// 空ではない
if (!empty($test1)) {
  処理したい内容
}

// 論理演算子
// AND
if ($test1 == 9 && $test2 == 1 ) {
  処理したい内容
}
// OR
if ($test1 == 9 || $test2 == 1 ) {
  処理したい内容
}

// 三項演算子
// 条件 ? 真 : 偽
$result = $test1 > 5 ? 'over5' : 'under5'

!=null
```

- for系

```php
$members = [
  'name' => '大谷',
  'height' => 190,
  'weight' => 95
]
// foreach
// バリューのみ表示
foreach($members as $member){
  echo $member;
}
// キーとバリュー表示
foreach($members as $key => $value){
  echo $key . 'は' . $value . 'です';
}

// for
for($i = 0; $i < 10; $i++){
  echo $i;
  if($i === 5){
    continue;
  }
  if($i >= 9){
    break;
  }
}

// while
$j = 0;
while($j < 5){
  echo $i;
  $j++;
}

// switch
$i = 1;
switch($i){
  case $i === 1: // 型も一致させた条件にしたい場合
    echo '結果は1です';
    break;
  case 2:  // 特に指定しなければ == と同じ
    echo '結果は2です';
    break;
  case 3:
    echo '結果は3です';
    break;
  default:
    echo 'いずれにも当てはまりません';
}
```

- 関数

```php
function 関数名(引数1, 引数2 ){
  処理内容
  return 戻り値;
}

//関数の呼び出し
関数名();
```

- アロー演算子

プロパティやメソッドへのアクセスで使用

```php
<?php
 
class User {
    public $name;       // 名前
    public $age;        // 年齢
 
    function __construct(string $name, int $age)
    {
        // アロー演算子を使ってプロパティにアクセス
        $this->name = $name;
        $this->age = $age;
    }
}
 
$sample_user = new User("Yamada Taro", 25);
```

- html内記述

```php
# html内でのphpのif文記入例
<?php if($pageFlag === 0) : ?>
入力画面
<?php endif; ?>
```

#### 組込関数

- 利用頻度高い関数

```php
$text = 'あかさたな';

// バイト数で表示(日本語なら1文字あたり３バイト~6バイトになる)
echo strlen($text)
// マルチバイト文字数で表示(日本語でも1文字でカウント) mb = マルチバイト
echo mb_strlen($text)

// 置換
str_replace('な','なはまやら',$text); //あかさたなはまやら
// 分割
$text = '区切り文字列は、カンマです。';
explode('、',$text); // ['区切り文字列は', 'カンマです。']
// implode という組込関数もあり

// $textの中に'区切り'という文字列が何個あるか表示
preg_match('/区切り/',$text);

// 文字列抽出
substr('abcd', 1) // bcd

// 既存配列に値を追加
$array = ['赤','青']
array_push($array, '緑', '紫')
```

#### ファイル読込

```php
// エラーがあると処理が止まる
require '../ファイルパス/ファイル名';
// エラーがあっても続けられる処理は継続する
include '../ファイルパス/ファイル名';
```

- マジック定数

```php
//絶対パスでカレントディレクトリを表示
echo __DIR__; 
//絶対パスでファイルを表示
echo __FILE__;
```

- 便利コマンド

```php
var_dump($変数); //変数の型を確認可能
phpinfo();      //バージョンや設定情報を確認可能
exit;           //この行以降の処理を中断させる
```

### フォーム

```html
// form作成
<form method="GET" action="対象ファイル.php">
指名
<input type="text" name="対象変数">
<input type="submit" value="送信">
</form>

# input.php?you_name=入力名 のようにURLの末尾にGETメソッドの内容が表示される(?より後の値をクエリ、クエリストリングと読んだりする)
```

- スーパーグローバル変数 phpには9種類ある

```php
$_GET['your_name']
$_POST['your_name'] //post通信はURL末尾にクエリが表示されない
```

```sh
# laravelの起動
php artisan serve

# laravel使用可能コマンドの一覧
php artisan list
```

## 開発環境

XAMPP(windows)やMAMP(mac)といった開発環境がある

- mac

```sh
brew install mamp
```

## php.ini

- display_errors
  - デフォルトではOFFになっているが、Onにしておくことでエラー内容を表示することが可能になる

[参考公式ドキュメント](https://www.php.net/manual/ja/opcache.configuration.php#ini.opcache.jit)
