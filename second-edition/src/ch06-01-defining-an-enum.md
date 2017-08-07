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

Defining an enum with variants like the ones in Listing 6-2 is similar to
defining different kinds of struct definitions except the enum doesn’t use the
`struct` keyword and all the variants are grouped together under the `Message`
type. The following structs could hold the same data that the preceding enum
variants hold:

```rust
struct QuitMessage; // unit struct
struct MoveMessage {
    x: i32,
    y: i32,
}
struct WriteMessage(String); // tuple struct
struct ChangeColorMessage(i32, i32, i32); // tuple struct
```

But if we used the different structs, which each have their own type, we
wouldn’t be able to as easily define a function that could take any of these
kinds of messages as we could with the `Message` enum defined in Listing 6-2,
which is a single type.

There is one more similarity between enums and structs: just as we’re able to
define methods on structs using `impl`, we’re also able to define methods on
enums. Here’s a method named `call` that we could define on our `Message` enum:

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
        // method body would be defined here
    }
}

let m = Message::Write(String::from("hello"));
m.call();
```

The body of the method would use `self` to get the value that we called the
method on. In this example, we’ve created a variable `m` that has the value
`Message::Write("hello")`, and that is what `self` will be in the body of the
`call` method when `m.call()` runs.

Let’s look at another enum in the standard library that is very common and
useful: `Option`.

### The `Option` Enum and Its Advantages Over Null Values

In the previous section, we looked at how the `IpAddr` enum let us use Rust’s
type system to encode more information than just the data into our program.
This section explores a case study of `Option`, which is another enum defined
by the standard library. The `Option` type is used in many places because it
encodes the very common scenario in which a value could be something or it
could be nothing. Expressing this concept in terms of the type system means the
compiler can check that you’ve handled all the cases you should be handling,
which can prevent bugs that are extremely common in other programming languages.

Programming language design is often thought of in terms of which features you
include, but the features you exclude are important too. Rust doesn’t have the
null feature that many other languages have. *Null* is a value that means there
is no value there. In languages with null, variables can always be in one of
two states: null or not-null.

In “Null References: The Billion Dollar Mistake,” Tony Hoare, the inventor of
null, has this to say:

> I call it my billion-dollar mistake. At that time, I was designing the first
> comprehensive type system for references in an object-oriented language. My
> goal was to ensure that all use of references should be absolutely safe, with
> checking performed automatically by the compiler. But I couldn't resist the
> temptation to put in a null reference, simply because it was so easy to
> implement. This has led to innumerable errors, vulnerabilities, and system
> crashes, which have probably caused a billion dollars of pain and damage in
> the last forty years.

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
