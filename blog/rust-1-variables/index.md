---
layout: post
title: "[ Rust🦀 ] Rust 의 보편적 기능 "
description: Rust 의 보편적 기능들을 알아보자
project-header: true
type: "Development"
blog: true
text: true
author: "Donghyun Kim"
post-header: true
header-img: "img/rust-crab.png"
order: 1
---

> Rust 의 언어로서의 보편적인 기능을 알 수 있다.

# Rust 의 언어로서의 기본적 속성

- 변수와 **불변성**
- 데이터 타입
- 함수
- 주석
- 제어문

Rust는 다른 언어와 같이 변수 / 함수 / 주석 / 제어문 등과 같은 기본적인 요소들을 가지고 있다.\
이 중, Rust의 특이점이라 하면, 변수의 **불변성/가변성**이 존재한다는 점이다. 

# 변수와 불변성 ( variable and immutable )

Rust 에서는 변수를 `let` 키워드로 선언한다.\
`let x` 명령을 통해 변수 x 를 선언하게 되면, 변수 `x`는 기본적으로 **불변성(immutable)** 의 성질을 가지게 된다.\
즉, 변수 `x`의 값을 더이상 바꿀 수 없다는 뜻이다.

변수의 값을 바꾸고 싶다면, `let` 다음에 `mut`키워드를 붙여주면 되는데, `let mut x`의 꼴이 된다.

{% highlight js %}
fn main() {
    let x = 1;
    let mut y = 1;
    x = 2; // compile error
    y = 2;
    println!("{} {}", x ,y);
}
{% endhighlight %}
위 코드를 실행하게 되면, 아래외 같은 컴파일 에러가 발생하게 된다.

{% highlight js %}
error[E0384]: cannot assign twice to immutable variable `x`
 --> src/main.rs:5:5
  |
3 |     let x = 1;
  |         -
  |         |
  |         first assignment to `x`
  |         help: make this binding mutable: `mut x`
4 |     let mut y = 1;
5 |     x = 2; // compile error
  |     ^^^^^ cannot assign twice to immutable variable

{% endhighlight %}

이처럼 변수의 기본적인 형태가 불변적인 것은, 값이 바뀌어야 하는 변수는 프로그래머가 명시적으로 표기하게 만들어,
변수 값이 바뀌는 데에서 발생하는 에러를 줄이기 위함으로 보인다.

# 데이터 타입 ( data type )

- `Scalar` (integer/float/boolean/char)
- `Compound` (tuple/array/struct/enum/trait)

Rust 에서 데이터 타입은 크게  `Scalar` 와 `Compound` 타입으로 나누어 진다.\
`Scalar` 은 하나의 값으로 표현되는 데이터 타입을 말하고,
`Compound` 은 여러 데이터의 쌍으로 표현되는 타입을 말한다.

`integer` : `i8`, `u8`, `i16`, `u16`, `i32`, `u32` \
`float` : `f32`, `f64` \
`char` : 1~5 byte ( default UTF-8 encoding )

# 함수 ( function )

Rust 에서 함수는 `fn` 키워드로 선언하게 된다.\
특이하게 함수명은 `snake_case` 로 제한된다.
예를 들어, 아래와 같은 함수를 컴파일 하게 되면,
{% highlight js %}
fn HelloPrint() {
    println!("hello, world");
}

fn main() {
    HelloPrint();
}
{% endhighlight %}

아래와 같이 컴파일러가 자동으로 함수명을 `snake_case` 로 수정해 준다.
{% highlight js %}
warning: function `HelloPrint` should have a snake case name
 --> src/main.rs:2:4
  |
2 | fn HelloPrint() {
  |    ^^^^^^^^^^ help: convert the identifier to snake case: `hello_print`
  |
  = note: `#[warn(non_snake_case)]` on by default
{% endhighlight %}

함수는 `표현식(expression)`과 `구문(statement)`으로 이루어 지는데,
`표현식`은 반환값이 있고, `구문`은 반환값이 없다.
또한, `표현식`은 세미콜론이 붙지 않고, `구문`은 세미콜론이 붙는다.


예를 들어, 앞서 설명했던 `x = 1`과 같은 대입은 구문이다. \
반환하는 값이 없기 때문에, 일반적으로 다른 언어에서는 사용 가능 했던 `y = x = 1;` 과 같은 식의 사용이 불가능 하다.


{% highlight js %}
fn hello() {
    println!("hello");
}

fn one_is_return() -> i32 {
    1
}

fn x_is_return(x:i32) -> i32 {
    x
}
{% endhighlight %}
함수의 선언은 위와 같이 반환형이 있는 경우 `->` 으로 표기해 주고, 파라미터는 `x: i32`와 같이 데이터 타입을 명시해 주어야 한다.\
눈치가 빠른 분은 이미 알아챘겠지만, 반환값이 없는 `hello()` 는 세미콜론으로 끝나는 `println!();` 의 구문으로 종료되고,
반환값이 있는 나머지 함수들은 세미콜론이 없는 표현식으로 종료된다.



## 주석 ( comment )

다른 언어와 마친가지로 `//` 이나 `/**/` 으로 주석을 달 수 있으며,
문서화 주석이라는 것이 있다고 하는데, 이는 나중에 다루겠다.

## 제어문 ( control statement )

- `if else`
- `loop`
- `while`
- `for`

다른 언어와 마찬가지로 `if else` 문, `while` 문, `for` 문이 존재한다.

이 중, `for` 문의 형태는 C 스타일인 `for(;;)`은 지원하지 않으며,\
python 처럼 `for var in expression { }` 의 형태만 지원한다.

예를 들어, 1부터 10 까지 출력하고 싶으면 아래와 같이 하면 된다.

{% highlight js %}
fn main() {
    for x in 1..11 {
        println!("{}",x);
    }
}
{% endhighlight %}

## References

- [rust-lang-book-ko](https://rinthel.github.io/rust-lang-book-ko/foreword.html)


