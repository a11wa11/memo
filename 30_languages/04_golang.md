# go

https://tech.librastudio.co.jp/entry/2021/07/23/153304

## goenvのインストール

```sh
git clone https://github.com/syndbg/goenv.git ~/.goenv
```

* PATHを通す　※zshの場合

```sh
echo 'export GOENV_ROOT="$HOME/.goenv"' >> ~/.zshrc
echo 'export PATH="$GOENV_ROOT/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(goenv init -)"' >> ~/.zshrc
source ~/.zshrc
```

* GOPATHの設定

```sh
export GOPATH=$HOME/go
export GOENV_ROOT=$HOME/.goenv
export PATH="$GOENV_ROOT/bin:$PATH"
eval "$(goenv init -)"
export PATH="$GOROOT/bin:$PATH"
export PATH="$PATH:$GOPATH/bin"
export GO111MODULE=on // 1.13 以降は不要
```

* プロジェクトの作成

```sh
go mod init github.com/${GITHUB_USER}/${PROJECT_NAME}
```

* godocのインストール

```sh
go get golang.org/x/tools/cmd/godoc
```

* depのインストール

```sh
go get -u github.com/golang/dep/cmd/dep


# パッケージのインストール
dep ensure
```

* デバッグ

```sh
# gdb引数ありで実行
# パターン1
gdb プログラム名
(gdb) run --foo --bar

# パターン2
gdb --args プログラム名 --foo --bar
(gdb) run
```

* dlvパッケージの追加

```sh
go get -u github.com/derekparker/delve/cmd/dlv
```

* dlvの使い方
  * [参考1](https://budougumi0617.github.io/2018/04/08/debug-by-delve/)
  * [参考2](https://nishinatoshiharu.com/go-delve-usage/)

```sh
# デバッグ。引数は -- の後に追加
dlv exec ./myprogram -- somearg
# mainから開始
b main.main
b main
```

<!-- 変数宣言、関数 -->

```go
package main

import (
	"fmt"
	"os/user" //二つ以上のライブラリのimportには()で囲めば連絡記載可能
	"time"
)

func add(x, y int) int { // 関数基本形
	return x + y
}

func main() {
	var j int = 1 //変数宣言+代入基本形
	k := 2    //↑変数宣言+代入省略形
	fmt.Println(j, k)

	r := add(5,6) //rという変数にadd関数の結果を代入している
	fmt.Println(r)
	fmt.Println("Hello awa", time.Now())
	fmt.Println(user.Current())
	var ( // 連続で変数宣言+代入したいとき
		i int = 1
		f64 float64 = 1.2
		s string = "test"
		t, f bool = true, false
	)
	xi := 3
	fmt.Println(i, f64, s, t, f, xi)
	fmt.Println("Hello" + "World")
	fmt.Println(string("Hello"[0]) + "World")
	fmt.Printf("%T", f64)
}

<!-- 型変換 -->
package main

import (
	"fmt"
	"strconv"
)

func main()  {
	var x int = 1
	xx := float64(x)
	fmt.Printf("%T %v %f\n", xx, xx, xx)
	//%T = 型, %v = 値
	//%fはfloatのこと
	var y float64 = 1.2
	yy := int(y)
	fmt.Printf("%T %v %d\n", yy, yy, yy)
	//%dはintのこと

	var s string = "999"
	i, _ := strconv.Atoi(s)
	fmt.Printf("%T %v", i, i)
}

<!-- 配列 -->
package main

import "fmt"

func main()  {
	var a [2]int // a変数に配列宣言
	a[0] = 100
	a[1] = 200
	fmt.Println(a)

	var b [2]int = [2]int{100, 200}
	b[1] = 700
	fmt.Println(b)

	var c []int = []int{100, 200}
	c = append(c, 300)
	fmt.Println(c)
}


<!-- スライス -->
package main

import "fmt"

func main()  {
	n:= []int{1,2,3,4,5,6}
	fmt.Println(n)
	fmt.Println(n[2])
	fmt.Println(n[2:4])
	fmt.Println(n[:2])
	fmt.Println(n[2:])
	fmt.Println(n[:])

	n[2] = 100
	fmt.Println(n)

	var board = [][]int{
		[]int{0,1,2},
		[]int{3,4,5},
		[]int{6,7,8},
	}
	fmt.Println(board)

	board = append(board, []int{22,33,44})
	fmt.Println(board)
}

<!-- スライス2 makeとcap -->
package main

import "fmt"

func main()  {

	n := make([]int, 3, 5)
	fmt.Printf("len=%d cap=%d value=%v\n", len(n), cap(n), n) //cap ⇨　スライスに入る容量
	n = append(n, 0, 0)
	fmt.Printf("len=%d cap=%d value=%v\n", len(n), cap(n), n)
	n = append(n, 1, 2, 3, 4, 5)
	fmt.Printf("len=%d cap=%d value=%v\n", len(n), cap(n), n)

	a := make([]int, 3) // 長さも容量も両方3となる
	fmt.Printf("len=%d cap=%d value=%v\n", len(a), cap(a), a)

	b := make([]int, 0) // 長さも容量も両方3となる
	var c []int
	fmt.Printf("b = len=%d cap=%d value=%v\n", len(b), cap(b), b) //メモリの確保をする
	fmt.Printf("c = len=%d cap=%d value=%v\n", len(c), cap(c), c) //メモリの確保はしていない

	c = make([]int, 0, 5)
	for i := 0; i < 5; i++{
		c = append(c, i)
		fmt.Println(c)
	}
	fmt.Println(c)
}

<!-- map -->
package main

import "fmt"

func main()  {

	m := map[string]int{"apple": 100, "banana": 200}
	fmt.Println(m)
	fmt.Println(m["apple"])
	m["banana"] = 300
	fmt.Println(m)
	m["new"] = 500
	fmt.Println(m)

	fmt.Println(m["nothing"]) //何もなければ0が定義される

	v, ok := m["apple"]  //okはboolean型で値が入っているかの結果を返す
	fmt.Println(v, ok)

	v2, ok2 := m["nothing"]
	fmt.Println(v2, ok2)

	m2 := make(map[string]int)
	m2["pc"] = 5000
	fmt.Println(m2)

	var m3 map[string]int
	//m3["pc"] = 5000 // ↑で宣言はできているが、メモリ上にいれるmapがないのでpanicのエラーになる
	fmt.Println(m3)

	var s []int
	if s == nil{
		fmt.Println("Nil")
	}

<!-- byte -->
package main

import "fmt"

func main()  {
	b := []byte{72, 73}
	fmt.Println(b)
	fmt.Println(string(b))

	c := []byte("HI")
	fmt.Println(c)
	fmt.Println(string(c))

}


<!-- 関数 -->
package main

import "fmt"

func add1(x int, y int) int { //３番目のintは戻り値
	return x + y
}

func add2(x, y int) int { // x,yともに同じ型なら()内の最後の宣言だけに省略可能
	return x + y
}

func add3(x, y int) (int, int) {
	return x + y, x -y
}

func cal(price, item int) (result int){ // resultという変数を宣言し、戻り値としても設定
	result = price * item
	return //戻り値でresultを返すことを宣言しているのでreturnだけでもresultを返してくれる
	//return result　もちろん指定してもOK
}

func main()  {
	r := add1(10, 30)
	fmt.Println(r)

	rr := add2(10, 90)
	fmt.Println(rr)

	r1, r2 := add3(10 , 20)
	fmt.Println(r1, r2)

	rx := cal(17, 26)
	fmt.Println(rx)

	f := func(x int){ // 関数を定義して、変数として格納する
		fmt.Println("inner func", x)
	}
	f(1) //　関数を実行↑

	func(x int){
		fmt.Println("inner func", x)
	}(33) // 末尾に(33)で宣言と同時に実行が可能
}



<!-- クロージャー -->
package main

import "fmt"

func incrementGenerator() (func() int) {
	x := 0
	return func() int {
		x++
		return x
	}
}

func circleArea(pi float64) func(radius float64) float64 {
	return func(radius float64) float64{
		return pi * radius * radius
	}
}

func main()  {
	counter := incrementGenerator()
	fmt.Println(counter())
	fmt.Println(counter())
	fmt.Println(counter())

	c1 := circleArea(3.14)
	fmt.Println(c1(2))
	fmt.Println(c1(3))

	c2 := circleArea(3)
	fmt.Println(c2(2))
	fmt.Println(c2(3))

}


<!-- 可変超引数 -->
package main

import "fmt"

func foo(params ...int){ // 可変超引数の定義の仕方
	fmt.Println(len(params))
	fmt.Println(params)
	for _, param := range params{
		fmt.Println(param)
	}
}

func main()  {
	foo()
	foo(10, 20)
	foo(10, 20, 30)

	s := []int{1, 2, 3}
	fmt.Println(s) // [1, 2, 3]が入っている
	foo(s...) // s...とすることで展開して引数を渡している
}

<!-- if文 -->
package main

import (
	"fmt"
)

func by2(num int) string{
	if num % 2 == 0{
		return "ok"
	} else {
		return "no"
	}
}

func main() {
	num := 9
	//基本if文
	if num%2 == 0 {
		fmt.Println("by 2")
	} else if num%3 == 0 {
		fmt.Println("by 3")
	} else {
		fmt.Println("else")
	}

	x, y := 10, 10
	//and文とor文
	if x == 10 && y == 10{
		fmt.Println("&&")
	}
	a, b := 20, 40
	if a == 20 || b == 30{
		fmt.Println("||")
	}
	//例1
	result := by2(10)
	if result == "ok" {
		fmt.Println("great1")
	}
	fmt.Println(result)

	if result2 := by2(10); result2 == "ok" { //この書き方で例1と同様の意味を持つ
		fmt.Println("great2")
	}
	//fmt.Println(result2) 例１と違う注意点としてresult2の変数はif文の中でしか使用できない

}

<!-- for文 -->
package main

import (
	"fmt"
)

func main() {
	//基本for文
	for i := 0; i < 10; i++ {
		if i == 3 {
			fmt.Println("continue")
			continue //　コンティニュー
		}

		if i > 5 {
			fmt.Println("break")
			break // break文
		}
		fmt.Println(i)
	}
	// 別の書き方1
	sum := 1
	for ; sum < 10; { //変数(sum)と処理内容(sum+=sum)をfor文の中では省略し、外でも定義できる
		sum += sum
		fmt.Println(sum)
	}
	fmt.Println(sum)
	// 別の書き方2
	x := 1
	for x < 10 { // 別書き方1とほぼ同じで;(コロン)を省略できる
		x += x
		fmt.Println(x)
	}
	fmt.Println(x)
}

<!-- range -->
package main

import "fmt"

func main() {
	l := []string{"python", "go", "java"}

	//len(配列)で配列の格納数に合わせる
	for i := 0; i < len(l); i++{
		fmt.Println(i, l[i])
	}

	for _, v := range l { // ↑と同じ。省略形
		fmt.Println(v)
	}

	m := map[string]int{"apple": 100, "banana": 200}
	for k, v := range m {
		fmt.Println(k, v)
	}
	// keyの方のみ出力する場合
	for k := range m {
		fmt.Println(k)
	}
	// valueの方のみ出力する場合
	for _, v := range m {
		fmt.Println(v)
	}

}
<!-- switch文 -->
package main

import "fmt"

func getOSName() string {
	return "mac"
}

func main() {
	os := getOSName()
	switch os { //switch基本文
	//switch os := getOSName(); os { //↑と同じ。変数宣言と代入を１行でできる。ただし、switch文の中でしか変数(os)は使えない
	case "mac":
		fmt.Println("Mac!")
	case "windows":
		fmt.Println("Windows!")
	default: //他言語のelse文と同等だがdefaultはなくてもOK
		fmt.Println("Default!")
	}

	switch { // switch文で条件式を書かず、caseで書いてもOK
	case os == "mac":
		fmt.Println("Mac!!!")
	}
}

<!-- defer -->
package main

import (
	"fmt"
	"os"
)

func foo(){
	defer fmt.Println("world foo")
	fmt.Println("Hello foo")
}

func defertest(){
	fmt.Println("run")
	// deferを複数実行した場合、3,2,1と後から順に実行される
	defer fmt.Println(1)
	defer fmt.Println(2)
	defer fmt.Println(3)
	fmt.Println("success")
}

func mytest(){
	file, _ := os.Open("./lesson.go") //ファイルを開く
	defer file.Close()  //ファイルを開いたら必ず閉じなければいけないため、よくdeferをセットで使う
	data := make([]byte, 102) // byte配列で読み込むために宣言。スライスだが、文字列を扱うbyteはbyte配列と言われる
	file.Read(data) //定義したfile変数で読み込み、byte配列にいれる
	fmt.Println(string(data)) //castでstring型に変換して出力
}

func main()  {
	defer fmt.Printf("world") // deferは遅延実行。関数内の全てを実行後の最後に実行する
	foo()
	defertest()
	fmt.Println("Hello")

	mytest()
}

<!-- log -->
package main

import (
	"fmt"
	"io"
	"log"
	"os"
)

func LoggingSettings(logFile string) {
	logfile, _ := os.OpenFile(logFile, os.O_RDWR|os.O_CREATE|os.O_APPEND, 0666) //どういったモードで開くかを設定
	multiLogFile := io.MultiWriter(os.Stdout, logfile) //標準出力とログ書き込みを同時に行う
	log.SetFlags(log.Ldate | log.Ltime | log.Llongfile) //logに書き込む形の指定
	log.SetFlags(log.Ldate | log.Ltime | log.Lshortfile) //logに書き込む形の指定.ファイル名表記を短くしている
	log.SetOutput(multiLogFile)
}

func main() {
	LoggingSettings("test.log")
	_, err := os.Open("nothing_file")
	if err != nil { // try exception的な基本文
		log.Fatalln("Exit")
	}

	log.Println("loggingtest") //log標準出力の基本文
	log.Printf("%T %v", "test", "test") //fmtと同じように型などを出力できる

	log.Fatalln("error") //Printlnとほぼ同じだが、プログラム終了するので、後続は動かない
	log.Fatalf("%T %v", "test", "test") //fmtと同じように型などを出力できる
	fmt.Println("aaa") //↑でFatallnなどでプログラムが終了するため、本ラインは動作しない
}

<!-- エラーハンドリング -->
package main

import (
	"fmt"
	"log"
	"os"
)

func main() {
	file, err := os.Open("./lesson.go")
	if err != nil{
		log.Fatalln("ERROR!")
	}
	defer file.Close() // エラーでない場合はファイルが開かれている状態なので、defer(遅延実行)で閉じる
	data := make([]byte, 25) //byte配列.25文字分
	// ↓errイニシャライズが２回目だが、複数の変数のイニシャライズの場合1つ以上初出の変数があれば成立する。
	count, err := file.Read(data) //fileはlesson.go、開く量はdataで定義。
	if err != nil{
		log.Fatalln("ERROR!!!!")
	}
	fmt.Println(count)
	fmt.Println(string(data))

	//err = os.Chdir("test")
	//if err != nil{
	// 上記２行を１行で表示↓
	if err = os.Chdir("test"); err != nil{
		log.Fatalln("ERROR")
	}
}
```


[go mod完全に理解した](https://zenn.dev/optimisuke/articles/105feac3f8e726830f8c)
[【Go】パッケージ/モジュールやgo modコマンドについてまとめ
Golang](https://blog.framinal.life/entry/2021/04/11/013819)
[Go1.16からの go get と go install について](https://qiita.com/eihigh/items/9fe52804610a8c4b7e41)
[Go 1.17連載が始まります: コンパイラとgo mod ](https://future-architect.github.io/articles/20210810a/)
[delve](https://github.com/go-delve/delve)
[Golangのデバッガdelveの使い方](https://qiita.com/minamijoyo/items/4da68467c1c5d94c8cd7)
[Goのバージョン管理について](https://yyh-gl.github.io/tech-blog/blog/go-versions/)
[【goenv】GOPATH が変わらないときの対処法](https://yyh-gl.github.io/tech-blog/blog/gopath/)
[grafana](https://github.com/grafana/grafana)
