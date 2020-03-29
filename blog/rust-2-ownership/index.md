---
layout: post
title: "[ Rust🦀 ] Rust 의 소유권 "
description: Rust 의 소유권 개념에 대해 알아보자
project-header: true
type: "Development"
blog: true
text: true
author: "Donghyun Kim"
post-header: true
header-img: "img/rust-crab.png"
order: 2
---

> Rust 의 언어로서의 보편적인 기능을 알 수 있다.

이전 포스트에서 Rust 의 기본적인 속성들을 알아 보았다.
이번 포스트에서는 Rust 의 핵심 개념이라고 할 수 있는 `소유권` 에 대해 알아보자.

# 소유권이 뭔가요? ( Ownership )

우리는 이전에 사용하던 언어들을 쓰면서, 변수의 유효성에 따른 오류를 많이 겪어왔다.

- 동적 할당을 너무 많이 사용하여 생기는 `memory leak`
- memory leak 을 해결하기 위해 JAVA 에서 도입한 `GC(Garbage Collector)`
- 동일한 heap 영역을 가르키고 있는 변수들 때문에 생기는 `Dangling Pointer`
- 유효한 값을 가르키고 있지 않은 포인트 들 때문에 생기는 `Exception` 들 ..

Rust 는 이러한 문제들을 해결하기 위해 `소유권` 이라는 개념을 도입했다.
\
\
소유권이란, 변수가 어떤 값을 **소유** 할 수 있는 것을 말한다. \
앞서, Rust 의 소유권에 대한 규칙을 알아보자.


1. 러스트의 각각의 값은 해당값의 오너(owner)라고 불리우는 변수를 갖고 있다.
2. 한번에 딱 하나의 오너만 존재할 수 있다.
3. 오너가 스코프 밖으로 벗어나는 때, 값은 버려진다(dropped).

\
Rust 는 이러한 `소유권` 을 통해, JAVA 같은 별도의 GC도 두지 않으면서, C 처럼 memory leak 의 위험도 없는 안정성을 구현해 냈다.

# 변수의 스코프 ( Scope )

타 언어와 동일하게, Rust 의 변수도 `스코프(Scope)` 를 가진다.
변수는 해당 스코프 내에서만 사용할 수 있으며, 스코프를 벗어날 시 자동으로 `Drop` 된다.

{% highlight js %}

{
    let s = String::from("hello"); // s는 여기서부터 유효합니다
}                                  // 이 스코프는 끝났고, s는 더 이상 
                                   // 유효하지 않습니다

{% endhighlight %}

# 변수와 값의 관계 : 이동 ( Move )

먼저 일반적으로 변수간 값을 복사하는 코드의 예제를 보자.

{% highlight js %}

fn main () {
    let x: i32 = 10;
    let y: i32 = x;

    println!("x : {}, y : {}", x, y);
}

{% endhighlight %}

위 코드는 y 의 값을 x 에 복사하는 것이고, 아무 문제 없이 동작한다.

그러면, 위와 같은 경우를 String 에 적용해 보자.

{% highlight js %}

fn main () {
    let x: String = String::from("hello, world!");
    let y: String = x;

    println!("x : {}, y : {}", x, y);
}

{% endhighlight %}

이전의 코드와 차이는 int 타입에서 String 인 타입으로 바뀐 것 뿐이므로, 이 코드도 정상적으로 동작하야 할 것 같지만, \
실제로 컴파일해보면, 아래와 같은 컴파일 오류가 발생한다.

{% highlight js %}

error[E0382]: borrow of moved value: `x`
 --> src/main.rs:6:32
  |
3 |     let x: String = String::from("hello, world!");
  |         - move occurs because `x` has type `std::string::String`, which does not implement the `Copy` trait
4 |     let y: String = x;
  |                     - value moved here
5 | 
6 |     println!("x : {}, y : {}", x, y);
  |                                ^ value borrowed here after move
  
{% endhighlight %}

위 오류에서 두 중요한 문구를 볼 수 있는데, 첫째는 **value moved here** 이고, 둘째는 **value borrowed here after move**  이다. \
두 문구가 바로 Rust 의 소유권 때문에 발생한 오류인데, 4번째 줄 `x = y` 에서 "hello, world!" String 에 대한 소유권이 x 에서 y 로 이동했 버고,\
그 이후 6번째 줄에서 x 는 String 에 대한 `소유권` 을 가지고 있지 않지만 출력하려 했기 때문에 발생한 오류이다.\
이처럼 소유권을 가진 변수를 1개로 유지함으로써, `Dangling Pointer` 의 참조에 따른 오류를 없앨 수 있고,\
소유권을 가진 변수가 Scope 을 벗어날 때 값도 함께 `Drop` 시킴으로써 `memory leak` 도 방지할 수 있는 것이다. \ 
\\

그렇다면, 첫번째 경우에 컴파일 오류가 나지 않았던 이유는 무었일까?\
차이는 바로, 첫번째는 `primitive` 형의 변수를 사용했고, 두번째는 `reference` 형의 변수를 사용했다는 것이다.\
\
이는 다음 문단에서 설명하겠다.
\
# 변수와 값의 관계 : 복사 ( Copy )

Rust 에서도 변수간 값을 대입할 때, 항상 소유권의 이동이 일어나는 것은 아니다. \
값의 소유권이 이동되지 않고, 값 자체가 복사되는 경우가 있는데, 이는 변수가 STACK 에 들어갈 수 있는 ( 컴파일 타임에 크기를 알 수 있는 )
`primitive` 한 값일 경우이다.

값이 복사되는 타입은 아래와 같다.

- u32와 같은 모든 정수형 타입들
- true와 false값을 갖는 부울린 타입 bool
- f64와 같은 모든 부동 소수점 타입들
- Copy가 가능한 타입만으로 구성된 튜플들. (i32, i32)는 Copy가 되지만, (i32, String)은 안됩니다.

또한 자신이 직접 만든 구조체에를 Copy 되게 할 수 도 있는데, 아래처럼 Copy trait 을 구현하면 된다. ( 자세한 내용은 나중에 다루겠다. )

{% highlight js %}

#[derive(Copy,Clone,Debug)]  // Copy trait 구
#[derive(Debug)]
struct mystruct {
    x: i32,
    y: i32
}
impl mystruct {
    fn new(_x: i32, _y: i32) -> mystruct {
        mystruct{x: _x, y: _y}
    }
}
fn main () {
    let a = mystruct::new(1,1);
    let b = a;          // Copy Trait 을 구현함으로써,
    print!("{:?}",a);   // 값을 이동시켰지만 Copy 되어 컴파일 오류 발생하지 x
}

{% endhighlight %}


## References

- [rust-lang-book-ko](https://rinthel.github.io/rust-lang-book-ko/foreword.html)
