---
title: "세 번째 배우는 Go - 파트 2: Tour of Go를 학습하며 남기는 기록과 예제"
date: 2021-12-18T14:45:45+09:00
draft: false
toc: false
images: [/images/gopher.jpg]
tags:
  - go
  - golang
categories:
  - golang
description: Tour of Go를 하면서 정리한 내용입니다. 
url: /go/2021/12/18/tour-of-go/
---
{{< image src="/images/gopher.jpg" position="center" style="border-radius: 8px;">}}

<br/>

### 패키지
모든 프로그램은 `main` 패키지에서 실행을 시작한다.  
패키지를 import할 때 import한 패키지 경로의 마지막 요소가 네임스페이스처럼 해당 패키지가 가진 공개된 메서드를 호출할 때 사용된다.  

```go
package main

import (
  "fmt"
  "math/rand"
)

func main() {
  rand.Seed(100)
  fmt.Println("내가 제일 좋아하는 숫자는 ", rand.Intn(10))
}
```

import할 때 괄호로 그룹을 짓는 형태를 `factored import`라고 하며, 이 스타일을 사용하는 것을 추천한다.  
패키지를 import한 후 패키지 내부에 대문자로 시작하는 이름을 가진 것만 공개(export)된다.  


<br/>

### 함수
함수의 매개 변수가 2개 이상이며 같은 Type인 경우 마지막 매개변수 뒤에만 Type을 선언하면 된다.  

```go
func add(x, y int) int {
  return x + y
}
```

그리고 함수는 여러개의 결과를 반환할 수 있다.  

```go
func swap(x, y string) (string, string, string) {
  return y, x, "good"
}
```

함수를 반환값 Type을 선언할 이름을 지정할 수 있다. 이 이름은 함수 안에서 변수처럼 다뤄진다.  
그리고 아무것도 없이 `return`해도 함수를 선언할 때 지정한 반환값 이름이 반환된다.  
이런 방식을 `naked return`이라고 하며, 긴 함수에서 사용할 경우 가독성을 떨어뜨릴 수 있다는 점을 주의하자.  

```go
func split(sum int) (x, y int) {
  x = sum * 4 / 9
  y = sum - x
  return
}
```

<br/>

### 변수
`var`를 이용해 변수를 선언한다. 그리고 변수명 다음에 Type을 명시한다.  
변수를 선언할 때 초기값을 지정할 경우 Type을 생략할 수 있다.  

```go
var i, j int = 1, 2
var c, python, java = true, false, "no!"
```

함수 안에서만 `:=`로 변수를 선언하면 암시적으로 Type을 유추하게 된다.  

```go
func main() {
  var i, j int = 1, 2
  k := 3
  c, python, java := true, false, "no!"
}
```

변수에 사용되는 Type은... `bool`, `string`, `int` 등이 있다.  
변수에 초기값을 선언하지 않은 경우 zero value가 할당된다.  
숫자 Type의 Zero value는 0, Boolean Type의 Zero value는 false, String Type의 Zero value는 ""(빈 문자열)이다.  

타입 변환을 하려면 변수를 Type명으로 감싸는 형태로 변환할 수 있다.  

```go
var i int = 42
var f float64 = float64(i)
```

숫자를 `:=` 또는 지정한 값에 의해 Type이 유추될 경우 그 정확도에 따라 `int`, `float64`, `complex128`이 된다.  

```go
func main() {
  i := 42
  f := 3.142
  g := 0.867 + 0.5i
  
  fmt.Printf("i의 타입은 %T\n", i)
  fmt.Printf("f의 타입은 %T\n", f)
  fmt.Printf("g의 타입은 %T\n", g)
}
```

상수는 `var` 대신 `const` 키워드로 선언하며 `:=`를 통해 선언될 수 없다.  

<br/>

### For
Go에는 반복문은 `for` 하나만 존재한다.  
`for`문의 기본 구조는 `;`으로 구분되는 세 가지 구성 요소를 갖는다.  

1. 초기화 구문;  
2. ;조건 표현;  
3. ;반복 후 구문  

'(1)초기화 구문'에서 짧은 변수가 선언될 수 있는데 이 변수는 `for`문 Scope 안에서만 유효하다.  
'(2)조건 표현'이 `false`가 되면 반복이 종료된다.   

```go
func main() {
  sum := 0
  for i := 0; i < 10; i++ {
    sum += i
  }
  fmt.Println(sum)
}
```

'(1) 초기화 구문'과 '(3) 반복 후 구문'은 필수가 아니며, 다른 언어에는 존재하는 `while`이 되고, 모두 생략할 경우 무한 루프를 의미한다.  

```go
for sum < 1000 {
  sum += sum
}

for {
  fmt.Println("무한궤도")
}
```

<br/>

### If
Go의 `if`는 소괄호를 사용하지 않는다는 점에서  `for`와 비슷하다.  
그리고 `if`문 조건식에도 짧은 변수를 선언할 수 있다. 이 변수도 `if`문 Scope 안에서만 유효하다.  

```go
func pow(x, n, lim float64) float64 {
  if v := math.Pow(x, n); v < lim {
    return v
  }
  return lim
}
```

<br/>

### Switch
연속적인 `if-else` 구문을 짧게 하기 위해 `switch` 구문을 사용할 수 있다.  
Go의 `switch`는 다른 언어와 다르게 모든 case를 실행하지 않고, 먼저 선택된 case 조건만 실행하고 멈춘다. (자동 break)  
즉, `switch`는 위에서 아래로 case를 평가해서 성공하는 case에서 바로 멈추는 방식이다.  

```go
today := time.Now().Weekday()

switch time.Saturday {
case today + 0:
  fmt.Println("오늘")
case today + 1:
  fmt.Println("내일")
case today + 2:
  fmt.Println("이틀 후")
default:
  fmt.Println("너무 나중이네")
}
```

`switch`의 조건문에 꼭 상수만 쓸 수 있는 것은 아니다. 아래와 같이 짧은 변수를 선언하고, 즉시 사용할 수 있다.  

```go
swaich os := runtime.GOOS; os {
case "darwin":
  fmt.Println("MAC OS")
case "linux":
  fmt.Println("Linux")
default:
  fmt.Printf("%s \n", os)
}
```

`switch`의 조건문은 생략 가능하다.  

```go
switch {
case today + 0 == time.Saturday:
  fmt.Println("오늘")
case today + 1 == time.Saturday:
  fmt.Println("내일")
default:
  fmt.Println("너무 나중이네")
}
```

<br/>

### Defer
`defer`문은 자신이 선언된 함수가 종료할 때까지 실행을 연기한다.  
함수 안에서 `defer`를 여러번 사용하면 스택에 쌓이고, 함수가 종료될 때 LIFO(Last In, First Out) 순서로 실행된다.  

```go
func main() {
  fmt.Println("counting")
  for i := 0; i < 10; i++ {
    defer fmt.Println(i)
  }
  fmt.Println("done")
}
```

<br/>

### Pointers
Go는 변수의 메모리 주소를 가리키는 포인터를 지원한다.  
`*T` 라는 타입으로 변수를 선언하면 `T` 타입의 값을 가리키는 포인터가 된다.  
포인터의 Zero value는 `nil`이다.  

```go
var p *int
```

기존 변수명에 `&`연산자를 앞에 붙이면 이 값에 대한 포인터를 생성한다.  

```go
i := 100
p = &i
```

포인터 변수에 대해 `*`연산자를 앞에 붙이면 이 포인터가 가리키는 실제 값을 나타낸다.  

```go
fmt.Println(*p)
*p = 101
```

<br/>

### Structs
**Struct**는 필드의 집합이며, 필드는 .(dot)으로 접근할 수 있다.  

만약 어떤 struct 타입의 포인터 변수가 `p`라면,  `*p`는 이 포인터가 가리키는 실제 값에 대한 접근이고, X필드에 접근하기 위해 `*p.X` 라고 작성하게 된다. 하지만 이 표기에서 `*`은 다소 번거롭기 때문에 단순하게 `p.X`라고 작성할 수 있다.  

struct 타입을 선언할 때 필드의 이름을 직접 명시해서 값을 할당할 수 있다. 값을 지정하지 않은 필드는 Zero value로 초기화된다.  
struct을 선언할 때부터 접두사 `&`을 붙이면 이 struct 값의 포인터가 반환된다.  

```go
type Vertex struct {
  X, Y int
}

var (
  v1 = Vertex{1, 2}
  v1 = Vertex{X: 1}
  v3 = Vertex{}
  p = &Vertex{1, 2}
)
```

<br/>

### Array
배열을 선언할 때 `[n]T` 타입은  `T(예시)`라는 타입의 값이 n개 있는 배열이라는 의미를 갖는다.  
이 배열의 길이(length)는 그 타입의 일부라서 한번 선언된 배열의 크기를 조정할 수는 없다.  

```go
var a [10]int
```

<br/>

### Slice
배열의 길이(length)는 고정되어 있지만, 이 배열에 대한 슬라이스를 만들면 배열의 요소들을 가변적으로 다룰 수 있다.  
`[]T` 타입같이 슬라이스를 선언하면, `T(예시)`라는 타입의 슬라이스를 의미한다.  

슬라이스 표현식은 아래와 같은 형태를 가지는데, 첫번째 요소(`low`)는 슬라이스의 시작 요소이며, 두번째 요소(`high`)를 제외하는 범위에 해당한다.  
이 요소(`low`, `high`)는 각각 생략될 수 있는데 `low`의 기본값은 0이고, `high`의 기본값은 배열의 길이이다.  

```go
a[low : high]
```

```go
primes := [6]int{1, 2, 3, 4, 5, 6}
var s []int = primes[1:]
```

슬라이스의 특정 요소를 수정하면 원본 배열의 요소도 수정된다.  
그래서 동일한 배열로부터 만들어진 슬라이스는 변경이 생길 경우 함께 반영된다.  

```go
names := [4]string{
  "john",
  "paul",
  "george",
  "ringo",
}

a := names[0:2]
b := names[1:3]
fmt.Println(names)  // [john paul] [paul george]

b[0] = "jonnung"
fmt.Println(names)  // [john jonnung george ringo
```

당연하게도(?) 슬라이스에 슬라이스를 포함 시킬 수 있다. (2차원 배열처럼)  

```go
board := [][]string{
  []string{"_", "_", "_"},
  []string{"_", "_", "_"},
  []string{"_", "_", "_"),
}
```

<br/>

### Slice literals
슬라이스 리터럴 표현식은 길이가 없는 배열 리터럴과 같다.  

```go
[]bool{true, true, false}
```

<br/>

### Slice length & capacity
슬라이스는 길이(length)와 용량(capacity)를 갖는다.  
슬라이스의 길이는 슬라이스가 가리키는 범위에 해당되는 요소의 개수이다.  
슬라이스의 용량은 슬라이스가 가리키는 원본 배열 요소의 개수이다.  

```go
s := []int{1, 2, 3, 4, 5}

fmt.Printf("Length: %d\n", len(s))
fmt.Printf("Capacity: %d", cap(s))
```

슬라이스의 Zero Value는 `nil`이며, nil 슬라이스의 길이와 용량은 0이다.  

<br/>

### make로 슬라이스 만들기
내장 함수 `make`로 동적 크기의 슬라이스를 만들 수 있다.  
`make`함수의 첫번째 인자는 슬라이스, 두번째 인자는 길이(length), 세번째 인자는 용량(capacity)을 지정할 수 있다.  

```go
a := make([]int, 5, 5) // len(a)=5, cap(a)=5
```

`make`는 슬라이스 리터럴이나 원본 배열에서 슬라이스를 생성하는 경우가 아닌 길이(length)와 용량(capacity)을 예상하거나 결정된 상태에서 슬라이스를 만들어야 하는 경우 사용하면 좋을 것 같다.  

<br/>

### 슬라이스에 요소 추가하기
내장 함수 `append`로 슬라이스에 새로운 요소를 추가할 수 있다.  
`append`의 첫번째 인자는 `T`라는 타입의 슬라이스, 두번째 인자부터 추가할 값이다.  
`append`의 호출 결과는 주어진 슬라이스의 모든 요소와 추가한 값을 포함하는 새로운 슬라이스가 된다.  

```go
var s []int  // len(s)=0, cap(s)=0

s = append(s, 0)  // [0], len(s)=1, cap(s)=1
```

<br/>

### Map
맵은 Key와 Value의 쌍이다.  
Map으로 선언된 변수는 nil 입니다. Nil Map에는 값을 넣을 수 없다.   
그래서 Nil Map을 쓰기 위해 make라는 내장함수로 초기화를 해야한다. 그렇게 빈(empty) Map 된다.  

```go
var m map[string]string
fmt.Println(m)  // map[]
// m["jonnung"] = "zzang" // panic: assignment to entry in nil map

m = make(map[string]string)
m["jonnung"] = "zzang"

fmt.Println(m)  // map[jonnung:zzang]
```

위 방법은 정석적이라면 좀 더 간편하게 Map 리터럴을 사용할 수 있다.  
Map 리터럴은 Struct 리터럴과 비슷하지만 Key를 갖는다.  

```go
var m = map[string]string{
  "foo": "bar"
}  // map[foo:bar]
```

<br/>

### Map 다루기

```go
// 맵에 요소를 추가하거나 업데이트하기
m[key] = elem

// 요소 찾기
elem = m[key]

// 요소 제거하기
delete(m, key)

// 두 개의 변수에 값을 할당해서 Key가 존재하는 지 확인하는 용도
// m맵에 key가 없다면, ok는 false이며, elem은 맵 요소의 타입의 Zero value가 된다.
elem, ok = m[key]
```

<br/>

### Range
`for`문에 `range`를 붙이면 슬라이스와 맵을 순회할 수 있다.  
`range`를 사용하면, 각 반복할 때마다 두 개의 값이 반환되는 데 첫 번째는 인덱스, 두 번째는 그 순서에 해당하는 값의 복사본이 된다.  

```go
var pow = []int{1, 2, 3, 4, 5}

for i, v := range pow {
  fmt.Printf("Index: %d, Value: %d \n", i, v)
}

// Index: 0, Value: 1 
// Index: 1, Value: 2 
// Index: 2, Value: 3 
// Index: 3, Value: 4 
// Index: 4, Value: 5 
```

`range`가 반환하는 인덱스와 값 중에 사용하지 않으려면 `_`에 할당해서 생략할 수 있다.  


<br/>

### Closure
Go 함수는 함수의 인자가 될 수 있고, 함수의 반환값이 될 수 있다.  
이 특징 때문에 함수 안에서 선언된 함수는 클로저가 될 수 있다.  

<br/>

### Method
Go는 클래스가 없다.  
하지만 Type(Struct 포함)에 메소드를 정의할 수 있는데 이 메소드는 단지  **receiver** 라는 인자가 있는 함수일 뿐이다.  
이 receiver는 `func`키워드와 메소드명 사이에 추가된다.  

```go
type Vertex struct {
  X, Y float64
}

func (v Vertex) Abs() float64 {
  return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

```go
type MyFloat float64

func (f MyFloat) Abs() float64 {
  if f < 0 {
    return float64(-f)
  }
  return float64(f)
}
```

<br/>

### Pointer Receiver
포인터를 리시버로 선언할 수 있다.  
리시버를 포인터 Type으로 선언하려면 `*Vertex`같은 형식이 된다.  
포인터 리시버가 있는 메소드는 리시버가 가리키는 값을 수정할 수 있다.  
그렇기 때문에 포인터 리시버가 (그냥) 값 리시버보다 더 자주 쓰인다.  
(값 리시버는 메소드 안에서 원본의 복사본으로 작동한다.)  

```go
type Vertex struct {
  X, Y float64
}

func (v *Vertex) Scale(f float64) {
  v.X = v.X * f
  v.Y = v.Y * f
}
```


포인터 리시버가 있는 메소드는 값 변수에 의해 메소드가 호출 되더라도 자동으로 포인터의 메소드로 호출하는 것으로 해석한다.  
아래 예제 코드에서 `v`가 값 변수이고, `p`변수가 포인터 변수이다.  

```go
var v Vertex
v.Scale(5)  // (&v).Scale(5)

p := &v
p.Scale(5)
```

포인터 리시버를 사용하는 이유는 두 가지가 있다.  
1. 메소드가 리시버가 가리키는 값을 수정할 수 있다.
2. 각 메소드가 호출될 때마다 값이 복사되는 문제를 피할 수 있다.

<br/>

### Interface
인터페이스는 메소드의 집합이다.  
인터페이스의 메소드를 구현하는 모든 값은 인터페이스 Type이 될 수 있다.  
인터페이스 Type을 사용한다는 것은 명시적인 `implementation`키워드가 없더라도 인터페이스가 가진 메소드를 구현함으로써 이 인터페이스를 구현했다는 것을 암시한다.  

인터페이스 Type의 값이 `nil`인 경우, 그 메소드는 `nil` 리시버로 호출된다.  
아래 경우 변수`i`는 `nil`이 아니지만, `t`는 `nil`이다.  

```go
type I interface {
  M()
}

type T struct {
  S string
}

func (t *T) M() {
  if t == nil {
    fmt.Println("<nil>")
    return
  }
  fmt.println(t.S)
}

func main() {
  var i I  // 인터페이스 값
  // i.M()  // 런타임 에러

  var t *T
  i = t
  i.M()
}
```

Go에서 nil 리시버로 메소드가 호출되는 것은 예외를 발생시키지 않지만, nil 인터페이스의 값에서 메소드를 호출하는 것은 런타임 에러가 발생한다. (당연히 구현되지 않은 메소드를 호출할 수는 없음)  

<br/>

### 빈 인터페이스
메소드가 없는 인터페이스 유형을 empty interface라고 한다.  

```go
interface{}
```

빈 인터페이스는 모든 Type의 값을 받을 수 있어서 알 수 없는 값을 처리하는데 이용된다.  

<br/>

### 타입 체크
빈 인터페이스로 선언된 값의 타입을 체크할 때 아래와 같은 방법을 사용할 수 있다.  

```go
var i interface{} = "hello"

s := i.(string)
fmt.Println(s)

s, ok := i.(float64)
if ok == true {
  fmt.Println(s)
} else {
  fmt.Println("errrr")
}
```

<br/>

### 타입 Switch 문
Type Switch문은 일반 Switch문에 Type을 명시해서 빈 인터페이스로 전달된 인자의 Type을 비교할 때 사용한다.  

```go
func do(i interface{}) {
  switch v := i.(type) {
  case int:
    fmt.Println("Int")
  case string:
    fmt.Println("String")
  default:
    fmt.Println("I don't know")
  }
}
```

<br/>

### Errors
`error` Type은 `fmt.Stringer`와 유사한 내장 인터페이스이다.  

```go
type error interface {
  Error() string
}
```
