---
title: "Go에서 String과 Bytes Slice 이해하기"
date: 2022-08-16T23:44:10+09:00
draft: false
toc: false
images: [https://raw.githubusercontent.com/egonelbre/gophers/master/sketch/fairy-tale/messenger-showing.png]
tags:
  - golang
categories:
  - programing language
description: Go에서 어떨 때 string 타입과 []byte 타입으로 선언해야 할지 고민하게 되면서 공부한 내용입니다. 이 글에서는 유니코드와 UTF-8 인코딩에 대한 기초적인 내용은 다루지 않으니 따로 학습하는 것을 추천합니다. 
url: /golang/2022/08/16/go-string-bytes-slice/
---

> 이 글에서는 유니코드와 UTF-8 인코딩에 대한 자세한 내용은 다루지 않으니, 미리 가볍게 학습해두는 것을 추천한다.

{{< image src="https://raw.githubusercontent.com/egonelbre/gophers/master/sketch/fairy-tale/messenger-showing.png" position="center" style="border-radius: 8px;">}}

Go 언어에서 소스 코드는 UTF-8로 인코딩된다. 

그래서 소스 코드 안에 직접 작성한 모든 문자열을 UTF-8으로 인코딩하기 때문에 소스 코드에 "가나다"라고 쓰면 이 문자열은 UTF-8로 인코딩된 문자열이 되는 것이다.

(* UTF-8은 유니코드 포인트를 나타내기 위해 1바이트에서 최대 4바이트까지 바이트 수가 가변적이다)

```go
for i, r := range "가나다" {
  fmt.Println(i, r)
}
fmt.Println(len("가나다"))

// 0 44032
// 3 45208
// 6 45796
// 9
```

위 코드의 for 문에서 `i` 변수는 "가나다" 각각의 유니코드 문자에 대한 바이트 위치(Index)를 가져오고, `r` 변수는 유니코드 코드 포인트(Decimal)를 가져온다.

`i`변수의 타입은  `int`, `r`변수의 타입은 `rune`인데 `rune`은 `int32` 타입의 별칭으로서 유니코드 포인트 하나를 담을 수 있다.

`r`변수에 담긴 유니코드 포인트를 실제 문자로 출력하고 싶으면 `string(r)`으로 감싸서 호출하면 된다. `string()` 함수는 유니코드 포인트를 해당하는 유니코드 문자열로 형변환을 해주는 역할을 한다.

위 코드의 실행 결과에서 `i` 변수의 값은 0, 3, 6 순서로 출력된다.  
그 이유는 "가", "나", "다"를 UTF-8으로 표현하는데 글자당 3바이트가 필요하기 때문이다. 
결국 "가나다"는 총 9바이트가 된다.

이 원리를 활용하면 문자열을 바이트 단위로 다룰 수도 있다. 

위에서 사용된 for 문을 살짝 변형하여 문자열의 길이(9바이트)만큼 바이트 단위로 출력해보자.  
아래 코드에서 사용한 내장 함수 `len()`는 `string` 값을 전달하면 바이트 길이를 반환하고, `fmt.Printf()`함수에 전달한 `%d`포맷은 바이트마다 UTF-8 숫자 값을 출력한다.

```go
foo := "가나다" // 9바이트
for i := 0; i < len(foo); i++ {
  fmt.Printf("%d ", foo[i])
}

// 234 176 128 235 130 152 235 139 164
```

이렇게 문자열을 바이트 단위로 다룰 수 있기 때문에 모든 바이트를 슬라이스에 담게 되면 이것이 바이트 슬라이스(`[]byte`)가 된다.

위에서 했던 것과 반대로 `string` 타입을 `[]byte` 타입으로 형변환 하는 방법은 다음과 같다.

```go
bar := []byte(foo)
fmt.Printf("%d", bar)
// [234 176 128 235 130 152 235 139 164]

```

이 결과로 알 수 있는 사실은 바이트 슬라이스는 단지 유니코드 포인트의 UTF-8 인코딩을 담은 바이트 리스트일 뿐이다.

아래 코드는 `bar` 바이트 슬라이스 변수에 담긴 9바이트 중 앞에 3바이트만 `string`타입으로 출력한 결과이다.

```go
bar2 := bar[0:3]
fmt.Printf("%s", bar2)
// 가
```

즉, 234, 176, 128 이라는 값은 UTF-8 인코딩이 "가"를 유니코드 포인트로 나타내기 위해 3바이트를 사용하고 있다는 것을 의미한다.

마지막으로 문자열을 `string` 타입으로 다루는 것과 `[]byte` 타입으로 다루는 것에 중요한 차이점은 값을 직접 조작할 수 있는지다.  
`[]byte` 타입은 값을 교체할 수 있고 크기 조절이 가능한 연속적인 바이트 리스트지만, `string`은 값을 수정할 수 없고 고정된 크기의 연속된 바이트 리스트이다.  
따라서 `string` 타입의 값은 언제나 새로운 문자열을 생성할 수밖에 없게 된다.

`string`타입은 이러한 불변의 특징 때문에  `map`의 키로 사용될 수 있고, `[]byte` 타입은 바이트 스트림을 처리하는 로우(raw) 바이트를 다룰 때 사용하기 좋다.
