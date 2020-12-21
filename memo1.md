<table>
<tr>

<th>機能</th>
<th>python</th>
<th>ruby</th>
</tr>
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
```
</td>
<td>
  
```ruby
names.each do|name|
  puts name
end
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

