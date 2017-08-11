## 열거형 정의하기

코드를 작성 할 때, 어떻게 열거형이 구조체 보다 더 적절하고 유용한지 살펴 볼 
것입니다. IP 주소를 다뤄야 하는 경우를 생각해 봅시다.
현재 IP 주소에는 두개의 주요한 표준이 있습니다: 버전 4와 버전 6 입니다. 
프로그램에서 IP 주소에 대해 다룰 때 이 두 가지가 유일한 경우들 입니다:
모든 가능한 값들을 *나열(enumerate)* 할 수 있으며, 이 경우를 열거라고 부를 수 
있습니다.

어떤 IP 주소는 버전 4나 버전 6이 될 수 있으며, 동시에 두 버전이 될 수는 
없습니다.
IP 주소의 속성을 보면 열거형 자료 구조가 적절합니다. 
왜냐하면, 열거형의 값은 variants 중 하나만 될 수 있기 때문 입니다.
버전 4나 버전 6은 근본적으로 IP 주소이기 때문에, 이 둘은 코드에서 모든 종류의 
IP 주소에 적용되는 상황을 다룰 때 동일한 타입으로 처리되는 것이 좋습니다.

`IpAddrKind` 이라는 열거형을 정의하면서 포함할 수 있는 IP 주소인 `V4` 과 `V6` 를
나열함으로써 이 개념을 코드에 표현할 수 있습니다.
이것들은 열거형의 *variants* 라고 합니다:

```rust
enum IpAddrKind {
    V4,
    V6,
}
```

`IpAddrKind` is now a custom data type that we can use elsewhere in our code.

### 열거형 값

아래 처럼 `IpAddrKind` 의 두개의 variants 에 대한 인스턴스를 만들 수 있습니다:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
let four = IpAddrKind::V4;
let six = IpAddrKind::V6;
```

열거형의 variants 는 열거형을 정의한 식별자에 의해 이름 공간이 생기며, 두개의 
콜론을 사용하여 둘을 구분할 수 있습니다. `IpAddrKind::V4` 와 `IpAddrKind::V6` 의
값은 동일한 타입이기 때문에, 이 방식이 유용 합니다: `IpAddrKind`
이제 `IpAddrKind` 타입을 인자로 받는 함수를 정의할 수 있습니다:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
fn route(ip_type: IpAddrKind) { }
```

그리고, variant 중 하나를 사용해서 함수를 호출 할 수 있습니다:

```rust
# enum IpAddrKind {
#     V4,
#     V6,
# }
#
# fn route(ip_type: IpAddrKind) { }
#
route(IpAddrKind::V4);
route(IpAddrKind::V6);
```

열거형을 사용하면 잇점이 더 있습니다. IP 주소 타입에 대해 더 생각해 볼 때, 
지금으로써는 실제 IP 주소 *데이터*를 저장할 방법이 없습니다; 단지 어떤 *종류* 
인지만 알 뿐 입니다. 5장에서 구조체에 대해 방금 공부 했다고 한다면, 이 문제를 
Listing 6-1 에서 보여지는 것 처럼 풀려고 할 것 입니다:

```rust
enum IpAddrKind {
    V4,
    V6,
}

struct IpAddr {
    kind: IpAddrKind,
    address: String,
}

let home = IpAddr {
    kind: IpAddrKind::V4,
    address: String::from("127.0.0.1"),
};

let loopback = IpAddr {
    kind: IpAddrKind::V6,
    address: String::from("::1"),
};
```

<span class="caption">Listing 6-1: `struct` 를 사용해서 IP 주소의 데이터와 
`IpAddrKind` variant 저장하기</span>

여기서 두 개의 필드를 갖는 `IpAddr` 를 정의 했습니다: `IpAddrKind` 타입(이전에 
정의한 열거형)인 `kind` 필드와 `String` 타입인 `address` 필드 입니다. 구조체에 
대한 두 개의 인스턴스가 있습니다.
첫번째 `home` 은 `kind` 의 값으로 `IpAddrKind::V4` 을 갖고 연관된 주소 데이터로
`127.0.0.1` 를 갖습니다. 두번째 `loopback` 은 `IpAddrKind` 의 다른 variant 인 
`V6` 을 값으로 갖고, 연관된 주소로 `::1` 를 갖습니다. `kind` 와 `address` 의 
값을 함께 사용하기 위해 구조체를 사용 했습니다. 그렇게 함으로써 variant 가 
연관된 값을 갖게 되었습니다.

각 열거형 variant 에 데이터를 직접 넣는 방식을 사용해서 열거형을 구조체의 일부로
사용하는 방식 보다 더 간결하게 동일한 개념을 표현할 수 있습니다. 
`IpAddr` 얼거형의 새로운 정의에서는 두 개의 `V4` 와 `V6` variant 는 연관된 
`String` 타입의 값을 갖게 됩니다:

```rust
enum IpAddr {
    V4(String),
    V6(String),
}

let home = IpAddr::V4(String::from("127.0.0.1"));

let loopback = IpAddr::V6(String::from("::1"));
```

열거형의 각 variant 에 직접 데이터를 붙임으로써, 구조체를 사용할 필요가 없어
졌습니다.

구조체 보다 열거형을 사용할 때 다른 장점이 있습니다: 각 variant 는 다른 타입과 
다른 양의 연관된 데이터를 갖을 수 있습니다. 버전 4 타입의 IP 주소는 항상 0 ~ 255
사이의 숫자 4개로 된 구성요소를 갖게 될 것 입니다. `V4` 주소에 4개의 `u8` 값을
저장하고, `V6` 주소는 하나의 `String` 값을 갖도록 표현할 수 있습니다, 구조체로는
이렇게 할 수 없습니다. 열거형인 이런 경우를 쉽게 처리합니다:

```rust
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);

let loopback = IpAddr::V6(String::from("::1"));
```

두가지 다른 종류의 IP 주소를 저장하기 위해 코드상에서 열거형을 정의하는 몇가지
방법을 살펴 봤습니다. 그러나, 누구나 알듯이 IP 주소와 그 종류를 저장하는 것은
흔하기 때문에, [표준 라이브러리에 사용할 수 있는 정의가 있습니다!][IpAddr]
<!-- ignore --> 표준 라이브러리에서 `IpAddr` 를 어떻게 정의하고 있는지 살펴봅시
다. 위에서 정의하고 사용했던 것과 동일한 열거형과 variant 를 갖고 있지만,
variant 에 포함된 주소 데이터는 두 가지 다른 구조체로 되어 있으며, 각 variant 
마다 다르게 정의하고 있습니다:

[IpAddr]: ../../std/net/enum.IpAddr.html

```rust
struct Ipv4Addr {
    // details elided
}

struct Ipv6Addr {
    // details elided
}

enum IpAddr {
    V4(Ipv4Addr),
    V6(Ipv6Addr),
}
```

이 코드에서 보듯이 열거형 variant 에 어떤 종류의 데이터라도 넣을 수 있습니다:
예를 들면 문자열, 숫자 타입, 혹은 구조체. 다른 열거형 조차도 포함할 수 있습니다!
또한 표준 라이브러리 타입들은 어떤 경우에는 해결책으로 생각한 것보다 훨씬 더
복잡하지 않습니다.

현재 스코프에 표준 라이브러리를 가져오지 않았기 때문에, 표준 라이브러리에 
`IpAddr` 정의가 있더라도, 동일한 이름의 타입을 만들고 사용할 수 있습니다.
타입을 가져오는 것에 대해서는 7장에서 더 살펴볼 것 입니다.

Listing 6-2 에 있는 열거형의 다른 예제를 살펴 봅시다: 이 예제에서는 각 variants
에 다양한 유형의 타입들이 포함되어 있습니다:

```rust
enum Message {
    Quit,
    Move { x: i32, y: i32 },
    Write(String),
    ChangeColor(i32, i32, i32),
}
```

<span class="caption">Listing 6-2: `Message` 열거형은 각 variants 가 다른 타입과
다른 양의 값을 저장함.</span>

이 열거형에는 다른 데이터 타입을 갖는 네 개의 variants 가 있습니다:

* `Quit` 은 연관된 데이터가 전혀 없습니다.
* `Move` 은 익명 구조체를 포함 합니다.
* `Write` 은 하나의 `String` 을 포함 합니다.
* `ChangeColor` 는 세 개의 `i32` 을 포함 합니다.

Listing 6-2 에서 처럼 variants 로 열거형을 정의하는 것은 다른 종류의 구조체들을
정의하는 것과 비슷합니다. 열거형과 다른 점은 `struct` 키워드를 사용하지 않는
다는 것과 모든 variants 가 `Message` 타입으로 그룹화 된다는 것 입니다.
아래 구조체들은 이전 열거형의 variants 가 갖는 것과 동일한 데이터를 포함할 수
있습니다:

```rust
struct QuitMessage; // 유닛 구조체
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String); // 튜플 구조체
struct ChangeColorMessage(i32, i32, i32); // 튜플 구조체
```

각기 다른 타입을 갖는 여러 개의 구조체를 사용한다면, 이 메세지 중 어떤 한가지를
인자로 받는 함수를 정의하기 힘들 것 입니다. Listing 6-2 에 정의한 `Message`
열거형은 하나의 타입으로 이것이 가능 했습니다.

열거형과 구조체는 한가지 더 유사한 점이 있습니다: 구조체에 `impl` 을 사용해서
메서드를 정의한 것 처럼, 열거형에도 정의할 수 있습니다. 여기 `Message` 열거형에
에 정의한 `call` 이라는 메서드가 있습니다:

```rust
# enum Message {
#     Quit,
#     Move { x: i32, y: i32 },
#     Write(String),
#     ChangeColor(i32, i32, i32),
# }
#
impl Message {
    fn call(&self) {
        // 메서드 내용은 여기 정의할 수 있습니다.
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

열거형의 값을 가져오기 위해 메서드 안에서 `self` 를 사용할 것 입니다.
이 에제에서 생성한 변수 `m` 은 `Message::Write("hello")` 값을 갖게 되고,
이 값은  `m.call()` 이 실행될 때, `call` 메서드 안에서 `self` 가 될 것입니다.

표준 라이브러리에 있는 매우 흔하게 사용하고 유용한 열거형을 살펴 봅시다:
`Option`.

### `Option` 열거형과 Null 값 보다 좋은 점들.

이전 절에서, `IpAddr` 열거형을 사용하여 작성한 프로그램에서는 러스트 타입 
시스템을 사용하여 데이터 뿐만 아니라 더 많은 정보를 담을 수 있는 방법을 살펴
보았습니다.

이번 절에서는 표준 라이브러리에서 열거형으로 정의된 또다른 타입인 `Option` 에 
대한 사용예를 살펴볼 것 입니다. `Option` 타입은 많이 사용되는데, 값이 있거나
없을 수도 있는 아주 흔한 상황을 나타내기 때문 입니다. 이 개념을 타입 시스템의
관점으로 표현하자면, 컴파일러가 발생할 수 있는 모든 경우를 처리했는지 체크 할 수
있습니다. 이렇게 함으로써 버그를 방지할 수 있고, 이것은 다른 프로그래밍 언어에서
매우 흔합니다.

프로그래밍 언어 디자인은 가끔 어떤 특성들이 포함되었는지의 관점에서 생각되기도
하지만, 포함되지 않은 특성들도 역시 중요 합니다. 러스트는 다른 언어에 있는지
null 특성이 없습니다. *Null* 은 값이 없다는 것을 표현하는 하나의 값 입니다.
null 을 허용하는 언어에서는, 변수는 항상 두 상태중 하나가 될 수 있습니다:
null 혹은 null 이 아님.

null 을 고안한 Tony Hoare 의 "Null 참조 : 10 억 달러의 실수" 에서 다음과 같이
말합니다:

> 나는 그것을 나의 10억 달러의 실수라고 생각한다. 그 당시 객체지향 언어에서
> 처음 참조를 위한 포괄적인 타입 시스템을 디자인 하고 있었다. 내 목표는
> 컴파일러에 의해 자동으로 수행되는 체크를 통해 모든 참조의 사용은 절대적으로 
> 안전하다는 것을 확인하는 것이었다. 그러나 null 참조를 넣고 싶은 유혹을 참을
> 수 없었다. 간단한 이유는 구현이 쉽다는 것이었다. 이것은 수없이 많은 오류와
> 취약점들, 시스템 종료를 유발 했고, 지난 40년간 10억 달러의 고통과
> 손실을 초래했을 수도 있습니다.

The problem with null values is that if you try to actually use a value that’s
null as if it is a not-null value, you’ll get an error of some kind. Because
this null or not-null property is pervasive, it’s extremely easy to make this
kind of error.

However, the concept that null is trying to express is still a useful one: a
null is a value that is currently invalid or absent for some reason.

The problem isn’t with the actual concept but with the particular
implementation. As such, Rust does not have nulls, but it does have an enum
that can encode the concept of a value being present or absent. This enum is
`Option<T>`, and it is [defined by the standard library][option]<!-- ignore -->
as follows:

[option]: ../../std/option/enum.Option.html

```rust
enum Option<T> {
    Some(T),
    None,
}
```

The `Option<T>` enum is so useful that it’s even included in the prelude; you
don’t need to import it explicitly.  In addition, so are its variants: you can
use `Some` and `None` directly without prefixing them with `Option::`.
`Option<T>` is still just a regular enum, and `Some(T)` and `None` are still
variants of type `Option<T>`.

The `<T>` syntax is a feature of Rust we haven’t talked about yet. It’s a
generic type parameter, and we’ll cover generics in more detail in Chapter 10.
For now, all you need to know is that `<T>` means the `Some` variant of the
`Option` enum can hold one piece of data of any type. Here are some examples of
using `Option` values to hold number types and string types:

```rust
let some_number = Some(5);
let some_string = Some("a string");

let absent_number: Option<i32> = None;
```

If we use `None` rather than `Some`, we need to tell Rust what type of
`Option<T>` we have, because the compiler can't infer the type that the `Some`
variant will hold by looking only at a `None` value.

When we have a `Some` value, we know that a value is present, and the value is
held within the `Some`. When we have a `None` value, in some sense, it means
the same thing as null: we don’t have a valid value. So why is having
`Option<T>` any better than having null?

In short, because `Option<T>` and `T` (where `T` can be any type) are different
types, the compiler won’t let us use an `Option<T>` value as if it was
definitely a valid value. For example, this code won’t compile because it’s
trying to add an `i8` to an `Option<i8>`:

```rust,ignore
let x: i8 = 5;
let y: Option<i8> = Some(5);

let sum = x + y;
```

If we run this code, we get an error message like this:

```text
error[E0277]: the trait bound `i8: std::ops::Add<std::option::Option<i8>>` is
not satisfied
 -->
  |
7 | let sum = x + y;
  |           ^^^^^
  |
```

Intense! In effect, this error message means that Rust doesn’t understand how
to add an `Option<i8>` and an `i8`, because they’re different types. When we
have a value of a type like `i8` in Rust, the compiler will ensure that we
always have a valid value. We can proceed confidently without having to check
for null before using that value. Only when we have an `Option<i8>` (or
whatever type of value we’re working with) do we have to worry about possibly
not having a value, and the compiler will make sure we handle that case before
using the value.

In other words, you have to convert an `Option<T>` to a `T` before you can
perform `T` operations with it. Generally, this helps catch one of the most
common issues with null: assuming that something isn’t null when it actually
is.

Not having to worry about missing an assumption of having a not-null value
helps you to be more confident in your code. In order to have a value that can
possibly be null, you must explicitly opt in by making the type of that value
`Option<T>`. Then, when you use that value, you are required to explicitly
handle the case when the value is null. Everywhere that a value has a type that
isn’t an `Option<T>`, you *can* safely assume that the value isn’t null. This
was a deliberate design decision for Rust to limit null’s pervasiveness and
increase the safety of Rust code.

So, how do you get the `T` value out of a `Some` variant when you have a value
of type `Option<T>` so you can use that value? The `Option<T>` enum has a large
number of methods that are useful in a variety of situations; you can check
them out in [its documentation][docs]<!-- ignore -->. Becoming familiar with
the methods on `Option<T>` will be extremely useful in your journey with Rust.

[docs]: ../../std/option/enum.Option.html

In general, in order to use an `Option<T>` value, we want to have code that
will handle each variant. We want some code that will run only when we have a
`Some(T)` value, and this code is allowed to use the inner `T`. We want some
other code to run if we have a `None` value, and that code doesn’t have a `T`
value available. The `match` expression is a control flow construct that does
just this when used with enums: it will run different code depending on which
variant of the enum it has, and that code can use the data inside the matching
value.
