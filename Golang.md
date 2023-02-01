# 變數宣告

## 基本宣告法

```go
package main

import "fmt"

var x = 123 // 全域變數

func main() {
	fmt.Println(&x)

	var x = 123 // 在`func區塊`。全新。
	fmt.Println(&x)

	// var x, y = 123, 100 // 因為x已經宣告過了，不能再用`var`。但以下卻可以
	x, y := 123, 100 // 在`func區塊`。此時x是上面宣告過的區域變數，而y是新的
	fmt.Println(&x, &y)

	if true {
		var x, y = 123, 100 // 在新區域`if區塊`。此時x跟y都是全新的。
		// 可以換成短變數宣告。

		fmt.Println(&x, &y)
	}

	fmt.Println(&x) // 脫離if區域，回到`func區塊`
}

/* 運行結果
0x11662a0
0xc000016080
0xc000016080 0xc000016088
0xc000016090 0xc000016098
0xc000016080
*/
```



## 常數宣告

```go
const PI = 3.14159
const HELLO = "Hello World"

//iota是希臘字符，在Golang中是關鍵字之一，用在宣告常數中，效果為數字遞增，iota本身數值從0開始，
const (
	A = iota       // 0
	B              // 1
	_              // 2 佔位符也會被計算
	C              // 3
	D = iota * 0.1 // 0.4 接續前面的 iota
	E              // 0.5
	F              // 0.6
	G              // 0.7
)
```



# 基本語句

## 輸出格式

```go
%d: digit   (10進位的數字)
%c: char    (字元)
%s: string  (字串)
%v: value   (值)
%+v 見下方
%#v 見下方

type Name struct {
	A string
	B bool
	C int
}
func main()  {
	fmt.Printf("%v	\n", Name{})
	fmt.Printf("%+v	\n", Name{})
	fmt.Printf("%#v	\n", Name{})
}
// { false 0}	
// {A: B:false C:0}	
// main.Name{A:"", B:false, C:0}

func main()  {
	a := 10
	fmt.Printf("a: %d\n", a)
	fmt.Println("a: ", a)

	s1 := "I"
	s2 := "am"
	s3 := "string"
    fmt.Printf("%s%s      %s\n",s1, s2, s3) //.NET string.format("",s1,s2,s3)
	fmt.Println(s1 + s2 + s3)
	fmt.Println(s1, s2, s3)

	fmt.Println("========")

	fmt.Print(s1 + s2 + s3)
	fmt.Print(s1, s2, s3)
}
/* result:
a: 10
a:  10
Iam      string
Iamstring
I am string
========
IamstringIamstring
*/
```



## IF

```go
func main() {
	B := 0
    
	if B == 1 {
		fmt.Println("True")
	} else {
		fmt.Println("False")
	}
}
```

## Switch

```go
var i int = 2
switch i {
case 1:
	fmt.Println("i is 1")
case 2:
	fmt.Println("i is 2")
case 3:
	fmt.Println("i is 3")
default:
	fmt.Println("i is not 1, 2, 3")

}
```

## For

```go
for i:=0; i<10; i++{
	fmt.Println(i)
}
```

## ForEach

```go
nums := []int{100, 99, 98}
for index, num := range nums {
	fmt.Println(index, num)
}

fruits := map[string]string{"a": "apple", "b": "banana"}
for index, fruit := range fruits {
	fmt.Println(index, fruit)
}
```

## Array

```go
func main() {
	b := [5]int{1, 2, 3} //單行宣告
	fmt.Println(b)

	c := [5]int{
		10,
		20,
		30,
		55, //使用多行宣告的話，最後一個元素要逗號
	}
    fmt.Println(c[:3])//從第n個元素開始列印到第m個元素(不包含m)，不給表示0

	d := [...]int{4, 6, 8, 10, 12, 14} //用...省略符號，讓go判斷長度
	fmt.Println(d[2:]) //從第n個元素開始列印到第m個元素(不包含m)，不給表示印到最後
}
//[1 2 3 0 0]
//[10 20 30]
//[8 10 12 14]
```

## Slice

- ### 類似list概念

- ### 不會實際儲存資料，而是透過指針指向存放位置

  - ![Pointer](https://i.imgur.com/WskKaXf.png)

- ### 包含三個部分

  - ##### ptr: 指到對應的陣列"位置"  

  - ##### len: 指向位置的元素個數

  - ##### cap: 容量

- ```go
  a := make([]int, 10) //設定 len:10。現在長度10了，容量雖然沒給，但最大容納長度當然不可能小於10吧，所以就是10了
  fmt.Println(a, len(a), cap(a), len(a) == 0, a == nil)
  
  b := make([]int, 5, 10) //設定 len:5、cap:10
  fmt.Println(b, len(b), cap(b), len(b) == 0, b == nil)
  
  var c = []int{} //初始化slice
  fmt.Println(c, len(c), cap(c), len(c) == 0, c == nil)
  
  var d []int //尚未實體化，此時等於nil
  fmt.Println(d, len(d), cap(d), len(d) == 0, d == nil)
  
  e := []string{"youtube.com", "facebook.com"} //直接賦值
  fmt.Println(e, len(e), cap(e), len(e) == 0, e == nil)
  
  /* result:
  [0 0 0 0 0 0 0 0 0 0] 10 10 false false
  [0 0 0 0 0] 5 10 false false
  [] 0 0 true false
  [] 0 0 true true
  [youtube.com facebook.com]
  */
  
  ```

- #### 新增資料進Slice

  - ```go
    x := []int{1, 2, 3}
    
    // append第一個變數為要塞入的原始Slice
    // append內的slice不會做異動，要用變數去承接
    y := append(x, 4, 5, 6)
    
    fmt.Println(x)
    fmt.Println(y)
    
    /* result:
    [1 2 3]
    [1 2 3 4 5 6]
    */
    ```

## Map

- ### 類似Dictionary<key,value>

  - ##### key；型態必須相同

  - ##### value: 型態必須相同

- ### 宣告時指定key、Value型態

- ```go
  var Number = map[string]int{ //string為key的type，int 為value
  	"零": 0,
  	"壹": 1,
  	"貳": 2,
  }
  Number["參"] = 3
  
  for key, value := range Number {
  	fmt.Println(key, value)
  }
  ```

## Struct

- ##### 在go中Struct屬於傳直呼叫，因此傳遞至其他函式中進行更新時是不會更新原來的struct

- ##### 需要透過 取址的方式(&struct)

```go
type Person struct {
	Age  int // 放Bag這個物件
	Name string
}

func main() {

	b := Person{
		Age:  16,
		Name: "Test",
	}

	b.UpdateP(99) //Call by Value 原b不變
	fmt.Println(b)

	(&b).UpdatePerson(-1) //call by reference，b會跟著變動
	fmt.Println(b)

}

func (p Person) UpdateP(n int) {
	p.Age = n
}

func (p *Person) UpdatePerson(n int) {
	(*p).Age = n
}

```

