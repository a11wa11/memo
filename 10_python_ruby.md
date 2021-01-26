# [閲覧ページはこちら(の方が好ましいです)](https://github.com/a11wa11/memo/blob/main/10_python_ruby.md)

<table>
<th>機能</th>
<th>python</th>
<th>ruby</th>
<tr>
<td>出力</td>
<td>

```python
print("abc")
```
</td>
<td>

```ruby
puts "abc"
```
</td>
</tr>
<tr>
<td>埋込</td>
<td>

```python
print("私の名前は%sです" % name)
```
</td>
<td>
  
```ruby
puts "私の名前は#{name}です"
```
</td>
</tr>
<tr>
<td>if</td>
<td>

```python
if a == b:
    print("OK")
```
</td>
<td>

```ruby
if a == b
  puts "OK"
end
```
</td>
</tr>
<tr>
<td>ifelse</td>
<td>

```python
if a == b:
    print("OK")
elif a > b:
    print("WARN")
else:
    print("NG")
```
</td>
<td>

```ruby
if a == b
  puts "OK"
elsif a > b
  puts "WARN"
else
  puts "NG"
end
```
</td>
</tr>
<tr>
<td>リスト/配列</td>
<td>

```python
names=["Tanaka","Yamada","Suzuki"]
```
</td>
<td>

```ruby
names=["Tanaka","Yamada","Suzuki"]
```
</td>
</tr>
<tr>
<td>繰り返し</td>
<td>

```python
for name in names:
    print(name)
for i, name in enumerate(names):
    print(i, name)
```
</td>
<td>

```ruby
names.each do|name|
  puts name
end

names.each_with_index do |name, index|
  puts "#{index}. #{name}"
end

# ブロック変数の記述は|配列の要素として扱う変数名, インデックス番号として扱う変数名|
```
</td>
</tr>
<tr>
<td>辞書/ハッシュ</td>
<td>

```python
user = {"name":"Suzuki","age":20}
```
</td>
<td>

```ruby
# 書き方その１
user = {"name" => "Suzuki","age" => 20}
# 書き方その2
user = {:name => "Suzuki", :age => 20}
```
</td>
</tr>
<tr>
<td>何もない</td>
<td>

```python
None
```
</td>
<td>
  
```ruby
nil
```
</td>
</tr>
<tr>
<td>メソッド</td>
<td>

```python
# 定義
def hello:
    print("HELLO")

def introduction(name):
    print("私は%sです。" % name)
    print("私は#{name}です。")

def add(x, y):
    return x + y

# 呼出
hello()
introduction("suzuki")
add(5, 6)
```
</td>
<td>

```ruby
# 定義
def hello
  puts "HELLO"
end

def introduction(name)
  puts "私は#{name}です。"
end

def add(x, y)
  return x + y
end

# 呼出
hello
introduction("suzuki")
add(5, 6)

# 別の書き方
def introduction_myself name, age
  puts "私は#{name}です。"
  puts "年齢は#{age}です。"
end

introduction_myself "suzuki", 20
```
</td>
</tr>
<tr>
<td>メソッド(キーワード引数使用)</td>
<td>

```python
# 定義
def introduction(name, age):
    print("私は%sです。" % name)
    print("年齢は%iです。" % age)
# 呼出
introduction(age=20, name="suzuki")
```
</td>
<td>

```ruby
# 定義
def introduction(name:, age:)
  puts "私は#{name}です。"
  puts "年齢は#{age}です。"
end
# 呼出
introduction(age: 20, name: "suzuki")
```
</td>
</tr>
<tr>
<td>クラス</td>
<td>

```python
class Menu:
    name = None

menu = Menu()
menu.name = "コーヒー"
```
</td>
<td>

```ruby
class Menu
  attr_accessor :name
end

menu = Menu.new
menu.name = "コーヒー"

```
</td>
</tr>
<tr>
<td>イニシャライズ</td>
<td>

```python
# 省略
def __init__(self, name):
    self.name = name

menu = Menu("コーヒー")
```
</td>
<td>

```ruby
# 省略
  def initialize(name)
    self.name = name
  end

menu = Menu.new("コーヒー")

```
</td>
</tr>
<tr>
<td>継承</td>
<td>

```python
class 新しいクラス名(もととなるクラス名):
```
</td>
<td>

```ruby
class 新しいクラス名 < もととなるクラス名
```
</td>
</tr>
<tr>
<td>and or</td>
<td>

```python
## and条件記法
A and B

## or条件記法
C or D
```
</td>
<td>

```ruby
## and条件記法
A && B
A and B
## or条件記法
C || D
C or D
```
</td>
</tr>
<tr>
<td>パッケージ管理システム</td>
<td>

```python
pip install ipython==7.9
```
</td>
<td>

```ruby
gem install rails -v 5.1.6
```
</td>
</tr>
<tr>
<td>機能</td>
<td>

```python
```
</td>
<td>

```ruby
```
</td>
</tr>
<tr>
<td>機能</td>
<td>

```python
```
</td>
<td>

```ruby
```
</td>
</tr>
</table>

<table>
  <tr>
    <th>ヘッダー1</th>
    <th>ヘッダー2</th>
  </tr>
  <tr>
    <td><code>print("test")</code></td>
    <td><code>puts "test"</code></td>
  </tr>
</table>
