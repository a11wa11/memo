# php

- [php](#php)
  - [php-fpm](#php-fpm)
  - [コマンド](#コマンド)
  - [構文](#構文)
    - [基本](#基本)
      - [組込関数](#組込関数)
      - [ファイル読込](#ファイル読込)
    - [設定関連](#設定関連)
      - [debug](#debug)
    - [フォーム](#フォーム)
    - [テンプレート](#テンプレート)
  - [開発環境](#開発環境)
  - [参考](#参考)
    - [php.ini](#phpini)

## [php-fpm](https://hackers-high.com/linux/php-fpm-config/)

PHPをFastCGIインターフェイス経由でサーバーに提供するためのプロセスマネージャ

- pm.max_children: プロセスマネージャで起動する最大子プロセス数を設定。プロセスマネージャが、子プロセスを起動する際に、この設定値を超えないようにします。同時リクエストの処理能力を向上させることができますが、同時に大量の子プロセスを起動すると、メモリ使用量が増加するため、サーバーのパフォーマンスに影響を与える可能性がある
- pm.start_servers: プロセスマネージャが起動するときに最初に起動する子プロセスの数を設定
- pm.min_spare_servers: プロセスマネージャが管理する子プロセスの最小数を設定。空きプロセスがこの値より少なくなった場合、プロセスマネージャは新しい子プロセスを起動します
- pm.max_spare_servers: プロセスマネージャが管理する子プロセスの最大数を設定。空きプロセスがこの値を超える場合、プロセスマネージャは子プロセスを終了させます

簡単にまとめると、pm.max_childrenは最大同時実行数を設定し、pm.max_spare_serversはアイドル状態で保持するプロセス数を設定するための設定です。pm.max_childrenが大きすぎる場合、システムの負荷が増加し、pm.max_spare_serversが小さすぎる場合、Webサーバのリクエスト処理が遅延する可能性があります。適切な設定を行うことで、Webサーバのパフォーマンスを最適化することができます。

## コマンド

```sh
# インストール済拡張機能一覧
php -m

# インストール可能な拡張機能一覧
ls /usr/src/php/ext

# PHPのインストールされたバージョンやビルド構成、PHPのモジュールの有効化状態、設定されているphp.iniファイルの場所、およびその中身などの詳細情報
php -i
php -i | grep -i パッケージ名 # 特定のおパッケージ名の確認
```

- php-fpmの再起動

```sh
kill -USR2 プロセス番号(php-fpm: master process)
    or
pkill -o -USR2 php-fpm # -oはoldest(一番古いという意味)
```

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

$test99 = 'テスト科目: ';
$test .= '社会' // 文字列結合する場合は.を使用 ※数字も文字列として扱われる
echo $test99; // テスト科目: 社会
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
function 関数名( 引数1, 引数2 ){
  処理内容
  return 戻り値;
}

//関数の呼び出し
関数名();
```

- クラス
  - アクセス修飾子
    - クラス内のプロパティやメソッドにアクセスする際に使用
      - public: どこからでもアクセス可能
      - private: そのクラス内からのみアクセス可能
      - protected: そのクラス内と継承したクラス内からのみアクセス可能
  - アロー演算子
    -プロパティやメソッドへのアクセスで使用

```php
<?php
// class クラス名( 引数1, 引数2 ) で定義
  // public: どこからでもアクセス可能な
  // private: そのクラス内からのみアクセス可能
  // protected: そのクラス内と継承したクラス内からのみアクセス可能
class User {
    public $name;       // 名前
    public $age;        // 年齢
    // コンストラクタ(初期化)
    function __construct(string $name, int $age){
        // アロー演算子を使ってプロパティにアクセス
        $this->name = $name;
        $this->age = $age;
    }
}
 
$sample_user = new User("Yamada Taro", 25); // インスタンス化
```

- 継承
  - 親クラス(基底クラス・スーパークラス)を引き継いで新しいクラスを作成することができる
  - 親クラスのプロパティやメソッドを引き継いで使用することができる
  - 子クラス(派生クラス・サブクラス)で親クラスの同名メソッドを上書きして使用すること(オーバーライド)ができる
  - extendsを使って継承できるのは1つのクラスだけ

```php
<?php
class 親クラス名 {

}
class クラス名(子クラス) extends 親クラス名 {
    function 関数名( 引数 ) {
        処理内容
    }
}
// final
final class クラス名 {              // クラスの継承を禁止（このクラスを基にしたサブクラスを作れない）
    final function 関数名( 引数 ) { // メソッドのオーバーライドを禁止（子クラスで上書きできない）
        処理内容
    }
}
```

- 抽象クラス
  - 抽象クラスを継承した子クラスで、抽象クラスの抽象メソッドを実装しなければならない
  - 抽象クラスは、abstractキーワードを使って宣言する
  - 普通クラスとインターフェースの中間のようなもの

```php
<?php
abstract class 抽象クラス名Abstract { // よくクラス名にAbstractをつける
    abstract function 抽象関数名( 引数 ); // 抽象メソッド
      処理内容
}
// 抽象クラスを継承した子クラス
class クラス名 extends 抽象クラス名Abstract {
    function 抽象関数名( 引数 ) { // 抽象メソッドの実装は必須
        処理内容
    }
}
```

- インターフェース
  - クラスが実装しなければならないメソッドを定義するためのもの
  - interfaceキーワードを使って宣言する
  - インターフェースを実装するクラスは、implementsキーワードを使って実装する

```php
<?php
interface class クラス名Interface { // よくクラス名にInterfaceをつける
    public function 関数名( 引数 ); // インターフェースのメソッド
    // 処理内容は書かない
}
// インターフェースを継承した子クラス
class クラス名 implements クラス名Interface1, クラス名Interface2 { // 複数のインターフェースを実装することが可能
    function 関数名( 引数 ) { // 抽象メソッドの実装は必須
        処理内容
    }
}
```

- trait
  - クラスの継承を使わずに、複数のクラスで共通のメソッドを使いたい場合に使用
  - useキーワードを使ってトレイトをクラスに適用する

```php
<?php
trait クラス名1Trait { // よくクラス名にTraitをつける
    public function 関数名1( 引数 ); // トレイトのメソッド
      処理内容
}

trait クラス名2Trait {
    public function 関数名2( 引数 );
      処理内容
}

// トレイトを適用したクラス
class クラス名 {
  use クラス名1Trait, クラス名2Trait; // 複数のトレイトを適用することが可能
    function 関数名( 引数 ) { // 抽象メソッドの実装は必須
        処理内容
    }
}

$sample = new クラス名("引数"); // インスタンス化
sample.関数名1(); // トレイトのメソッドを呼び出し
sample.関数名2(); // トレイトのメソッドを呼び出し
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

### 設定関連

```php
var_dump($変数); //変数の型を確認可能
phpinfo();      //バージョンや設定情報を確認可能
exit;           //この行以降の処理を中断させる
```

```sh
# PHPの現在の設定ファイルのパスおよび読み込まれている追加の設定ファイル（.iniファイル）の情報を表示
php --ini
```

- phpスクリプト実行

```sh
# インラインで指定されたコードを実行するオプション -r
php -r phpコード

# 例 PHPの設定情報をコマンドラインに表示
php -r 'phpinfo();'
```

#### debug

Xdebugの使い方

- インストール

```sh
pecl install xdebug
```

- php.iniへ反映

```php.ini
zend_extension="［サーバーのパス］php/modules/xdebug.so"
```

```sh
# $arr = array('Tokyo', 'Osaka', array('Yokohama', 'Kawasaki'));

$php sample.php

array(3) {
  [0]=>
  string(5) "Tokyo"
  [1]=>
  string(5) "Osaka"
  [2]=>
  array(2) {
    [0]=>
    string(8) "Yokohama"
    [1]=>
    string(8) "Kawasaki"
  }
}
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

### テンプレート

- Twig = 小枝という意味
  - シンプルで軽量なテンプレートエンジン
  - pythonのjinja2と似た構文

## 開発環境

XAMPP(windows)やMAMP(mac)といった開発環境がある

- mac

```sh
brew install mamp
```

## 参考

- [php公式ドキュメント](https://www.php.net/manual/ja/index.php)
- [php拡張モジュール](https://www.php.net/manual/ja/funcref.php)

### php.ini

- display_errors
  - デフォルトではOFFになっているが、Onにしておくことでエラー内容を表示することが可能になる

[参考公式ドキュメント](https://www.php.net/manual/ja/opcache.configuration.php#ini.opcache.jit)
