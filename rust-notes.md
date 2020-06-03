## Rust

### default attributes

```rust
#![warn(missing_debug_implementations, rust_2018_idioms, missing_docs)]
```

### The Rust Prelude

The prelude is the list of things that Rust automatically imports into every Rust program. It's kept as small as possible, and is focused on things, particularly traits, which are used in almost every single Rust program.

```rust
extern crate std; // inserts into the crate root of every crate
use std::prelude::v1::*; // inserts into every module
```

### `#[]` attribute

```rust
#[test]
fn test_something() {
  // something
}
```

### `{:?}` - debugging form

When used in functions like `println`, it can print out the value in "debug" form. For structs, annotate with `#[derive(Debug)]`.

### `{:#?}` - debugging form (prettier print)

Useful when having large structs to output

### `&, *` - borrowing and dereferencing

`&` operator borrows a reference and `*` dereferences it.

### `macro!` - macro

Anything that ends with a `!` can be considered a macro

### `r` - "raw string" syntax

The number of `#` can be zero to n, but they must be the same count in the beginning and end.

```rust
r#"
  something
  else
"#
```

### struct

```rust
struct Complex<T> {
  re: T,
  im: T
}

struct Vec2 {
  x: f64,
  y: f64,
}

let v1 = Vec2 { x: 1.0, y: 3.0 };
let v2 = Vec2 {
  x: 14.0,
  ..v1
} // struct update syntax
let v3 = Vec2 { ..v2 };
let Vec2 { x, y } = v3; // destructure

// field init shorthand
fn build_vec2(x: f64, y: f64) -> Vec2 {
  Vec2 {x, y}
}
```

### enum

Similar to algebraic data types in functional languages such as F#, OCaml, Haskell

```rust
enum Option<T> {
  None,
  Some(T)
}
```

### `///` - documentation comments

### generic function

Similar to function templates in C++.

> For any type `T` that implements the `FromStr` trait.

```rust
fn function_name<T: FromStr>() -> Option<(T, T)> {
  
}
```

### `as` - type conversion

```rust
1 as f64
```

### `()` - "unit" type

Used as a success type, similar to `void` in C, C++.

### `?` - operator

```rust
// instead of this
let output = match File::create(filename) {
    Ok(f) => { f }
    Err(e) => { return Err(e); }
};

// we can write this
let output = File::create(filename)?;
```

### `Result<()>` - shorthand		

Can be used as return type instead of `Result<(), std::io::Error>`

### `|value| { ... }` - closure expression

Similar to Ruby's block expression. Arguments and return type are inferred.

### usize and isize

`usize` is analogous to the `size_t` type in C and C++

`isize` is the signed analogue of the `usize` type, similar to `ssize_t` type in C and C++

### `0x, 0o, 0b` prefixes

Designates hexadecimal, octal, and binary respectively

### byte literals

Limited to ASCII values, from 0 to 127

Escaping characters

```rust
b'\'' /// single quote
b'\\' /// backslash
b'\n' /// newline
b'\r' /// carriage return
b'\t' /// tab
```

### bool type

Strict expressions

```rust
if x != 0 { ... } /// valid
if x { ... } /// invalid
```

Rust uses an entire byte for a bool value to be able to create a pointer

### Unicode character

```rust
'\u{HHHHHH}' /// where HHHHHH is a hexadecimal number between one and six digits long
```

### Explicit type in method call (consuming adaptors)

```rust
let vec = vec![1, 2, 3];
println!("{}", vec.iter().sum::<i32>()); // ::<> known as the turbofish operator
```

### raw pointers

```rust
*const T // immutable raw pointer
*mut T // mutable raw pointer

let mut num = 5;

let r1 = &num as *const i32;
let r2 = &mut num as *mut i32;
```

`unsafe` keyword is only needed when dereferencing raw pointers and reading the data.

```rust
unsafe {
    println!("r1 is: {}", *r1);
    println!("r2 is: {}", *r2);
}
```

### unsafe superpowers

* dereferencing a raw pointer
* calling an unsafe function or method
* accessing or modifying a mutable static variable
* implementing an unsafe trait
* accessing fields of `union`s

### memory safety bugs

* forgetting to `free`
* `free`ing too early
* double `free`

### Copy trait (ownership concept)

* When a type is annotated with the Copy trait, older variables are still usable after assignment
* A type cannot have both the Copy trait and Drop trait
* Simple scalar values (that do not require allocation) have Copy trait

### Drop trait (ownership concept)

* Rust will call drop at the end of scope to `free` memory
* similar to C++'s RAII (Resource Acquisition Is Initialization)

### generate local docs

```
$ cargo doc --open
```

### show stdout during test execution

```
cargo test -- --show-output
```

### run single test or multiple matching tests

```
cargo test <name or partial name of test(s)>
```

### `#[]ignore` attribute

Ignores test during execution

### run ignored tests

```
cargo test -- --ignored
```

### `#[cfg(test)]` annotation

Tells Rust to compile and run the test code only when you run `cargo test`, not when you run `cargo build`. This is only needed for unit tests. Conventionally, integration tests go in a separate `tests` directory.

### `tests` module

The convention is to create a `tests` module in each source file and write test functions in there

### integration tests - common module

Create `tests/common/mod.rs` instead of `tests/common.rs` if the common module is designed to share code across integration tests. It will not be part of test execution

### anonymous lifetimes

> "Use it when you can" - @jonhoo

### multiple lifetimes

> "Usually, you do not need multiple lifetimes" - @jonhoo

### type aliases

```rust
type Slot<K, V> = Option<((K, V), usize)>;
```

### deref coercion

* Occurs on arguments to functions and methods
* Only works on types that implements the `Deref` trait
* Use the reference (`&`)
* Rust will analyze the types and use `Deref::deref` as many times as necessary to get a reference to match the parameter’s type
* The number of times that `Deref::deref` needs to be inserted is resolved at compile time, so there is no runtime penalty for taking advantage of deref coercion
* Rust does deref coercion when it finds types and trait implementations in three cases:
  * From `&T` to `&U` when `T: Deref<Target=U>`
  * From `&mut T` to `&mut U` when `T: DerefMut<Target=U>`
  * From `&mut T` to `&U` when `T: Deref<Target=U>`

### `std::mem::drop` function

Different from the `drop` method in the `Drop` trait. Only to be used when you need to drop early

### `std::mem::replace` function

Replaces a value with another, returning the original value.

```rust
let new_node = Box::new(Node {
		elem: elem,
		next: mem::replace(&mut self.head, Link::Empty),
});
```

If wrapped in an `Option`, it may be better to use its method `take`:

```rust
let new_node = Box::new(Node {
		elem: elem,
		next: self.head.take(),
});
```

### IntoIter, Iter, IterMut

* IntoIter - `T`
* Iter - `&T`
* IterMut - `&mut T`

### Option's `as_mut`

Converts from `&mut Option` to `Option<&mut T>`

### Option's `as_ref`

Converts from `&Option` to `Option<&T>`. Note: adds a layer of indirection.

### Mutability in Rc and Arc

Mutability can be achieved in Rc via `RefCell` and Arc via `Mutex`, `RwLock`, or `Atomic`

### Interior Mutability Pattern

RefCell's `borrow_mut` allows for mutability within the context of an immutable `self`

```rust
fn send(&self, message: &str) {
		self.sent_messages.borrow_mut().push(String::from(message));
}
```

RefCell's borrow can then be used to get an immutable reference to `sent_messages`

```rust
mock_messenger.sent_messages.borrow().len()
```

### `Weak<T>`

Use `Rc::downgrade` on an instance of `Rc<T>` to return a smart pointer of type `Weak<T>`. When downgraded, `weak_count` will be used to keep track of how many `Weak<T>` references exist, but `weak_count` does not need to be 0 for the `Rc<T>` instance to be cleaned up.

Use `Ref::upgrade` to find out if the value has been dropped. This will return an `Option<Rc<T>>`.

`Weak` becomes useful for instances where there might be a reference cycle (e.g., tree representation where a Node struct has knowledge about its parent and children)

### `Rc::try_unwrap`

Returns the inner value if the `Rc` has exactly one strong reference.

### Refcell's `into_inner`

Consumes the `RefCell`, returning the wrapped value.

### Coerce normal pointer into raw pointer

```rust
let raw_tail: *mut _ = &mut *new_tail;
```

### crate roots

Usually `src/main.rs` or `src/lib.rs`. The contents of either of these two files form a module named `crate` at the root of the crate's module structure, known as the module tree.

### `pub` keyword

In structs, `pub` needs to be explicit for every field that needs to be public. In enums, however, `pub` makes all enum values public.

```
pub struct Breakfast {
		pub toast: String,
		fruit: String,
}

pub enum Appetizer {
		Soup,
		Salad,
}
```

## `use` keyword

When bringing in functions, provide the path to the function's module when calling `use`. When bringing in a struct, enum, or other items, provide the path to the actual item.

### abort on `panic!`

```toml
[profile.release]
panic = 'abort'
```

### trait implementation rule

A trait can be implemented if one of the following is true:

* trait is local to the crate, type can be local or external
* type is local to the crate, trait can be local or external

### default trait impl

Specify an empty `impl` for a trait to use the default implementation for said type.

Note: it is not possible to call the default implementation from an overriding implementation of the same method

### trait syntax variant for disambiguation

```rust
trait Next {
    fn next(&mut self) -> Option<u32>;
}

impl Next for Fibonacci {
    fn next(&mut self) -> Option<u32> {Some(0)}
}

fn main(){
    let mut seq = Fibonacci(0,1);

    print!("{}, ", Next::next(&mut seq).unwrap());
    print!("{}, ", <Fibonacci as Next>::next(&mut seq).unwrap());
}
```

### trait bound syntax

```rust
pub fn notify<T: Summary>(item: &T) {
    println!("Breaking news! {}", item.summarize());
}

// syntactical version
pub fn notify(item: &impl Summary) {
    println!("Breaking news! {}", item.summarize());
}

// same type implementing a trait
pub fn notify<T: Summary>(item1: &T, item2: &T) {
    // impl
}
```

### multiple trait bound syntax using `+`

```rust
pub fn notify(item: &(impl Summary + Display)) {
	  // impl
}

pub fn notify<T: Summary + Display>(item: &T) {
    // impl
}
```

### trait bound syntax using where clause

```rust
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {
	  // impl
}

fn some_function<T, U>(t: &T, u: &U) -> i32
    where T: Display + Clone,
          U: Clone + Debug
{
    // impl
}
```

### return types that implement trait

```rust
fn returns_summarizable() -> impl Summary {
    Tweet {
        username: String::from("horse_ebooks"),
        content: String::from(
            "of course, as you probably already know, people",
        ),
        reply: false,
        retweet: false,
    }
}
```

### blanket implementations

Implementations of a trait on any type that satisfies the trait bounds. For example, the standard library implements the `ToString` trait on any type that implements the `Display` trait.

### lifetime annotation examples

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}

struct ImportantExcerpt<'a> {
    part: &'a str,
}
```

Lifetimes on function or method parameters are called *input lifetimes*, and lifetimes on return values are called *output lifetimes*.

### three rules for lifetime elision

1. Each parameter gets its own lifetime parameter
2. If there is exactly one input lifetime parameter, that lifetime is assigned to all output lifetime parameters
3. If there are multiple input parameters, but one of them is `&self` or `&mut self` (because this is a method), the lifetime of `self` is assigned to all output lifetime parameters.

### lifetime variances

```rust
struct OutlivesExample<'a, 'b: 'a> {
    a_str: &'a str,
    b_str: &'b str,
}
```

* covariant - if 'b: 'a then T<'b>: T<'a>. This is the default for immutable data
* invariant - if 'b: 'a, nothing can be said about the relationship between T<'b> and T<'a>. This can happen for one of two reasons:
  * if the lifetime is present "inside" some sort of mutable context -- whether a &mut reference, or interior mutability like Cell/RefCell/Mutex
  * if the lifetime is used in multiple spots where the variances conflict
* contravariant - if 'b: 'a then T<'a>: T<'b>. This is uncommon and only shows up in parameters to fn pointers

Changing the variance of a parameter (lifetime or type) from covariant to anything else, or from contravariant to anything else, is a BREAKING CHANGE. If you're following semver, it can only be done with a new major version.

### `std::env::args`

Returns an iterator of the command line arguments passed in

```rust
let args: Vec<String> = std::env::args().collect();
```

### `std::env::args_os`

Use in place of `std::env::args` if any of the arguments contain invalid Unicode. This will return an iterator with `OsString` values instead of `String` values.

### `std::env::var`

Use to retrieve environment variable values.

```rust
env::var("CASE_INSENSITIVE")
```

### `Box<dyn Error>`

Any type that implements the `Error` trait. `dyn` keyword is short for dynamic.

### closures capturing environment

Closures can capture the environment in three ways:

* `FnOnce` consumes the variables it captures from its enclosing scope (takes ownership)
* `FnMut` can change the environment because it mutably borrows values
* `Fn` borrows values from the environment immutably

You can force a closure to use `FnOnce` by using the `move` keyword

```rust
fn main() {
    let x = vec![1, 2, 3];
    let equal_to_x = move |z| z == x;
    let y = vec![1, 2, 3];
  
    assert!(equal_to_x(y));
}
```

### zero-cost abstractions

This is analogous to how Bjarne Stroustrup, the original designer and implementor of C++, defines *zero-overhead* in “Foundations of C++” (2012):

> In general, C++ implementations obey the zero-overhead principle: What you don’t use, you don’t pay for. And further: What you do use, you couldn’t hand code any better.

### bounded parametric polymorphism

Rust instead uses generics to abstract over different possible types and trait bounds to impose constraints on what those types must provide.

### trait objects (via dynamic dispatch, incurs runtime cost)

```rust
pub struct Screen {
    pub components: Vec<Box<dyn Draw>>,
}
```

This defines a struct named `Screen` that holds a vector named `components`. This vector is of type `Box<dyn Draw>`, which is a trait object; it’s a stand-in for any type inside a `Box` that implements the `Draw` trait.

This works differently from defining a struct that uses a generic type parameter with trait bounds. A generic type parameter can only be substituted with one concrete type at a time, whereas trait objects allow for multiple concrete types to fill in for the trait object at runtime. Example: this restricts us to a `Screen` instance that has a list of components all of type `Button` or all of type `TextField`, but not a mixture of both.

```rust
pub trait Draw {
    fn draw(&self);
}

pub struct Screen<T: Draw> {
    pub components: Vec<T>,
}
```

### `_` does not bind to anything

```rust
let s = Some(String::from("Hello!"));

if let Some(_) = s { // s is not moved
    println!("found a string");
}

println!("{:?}", s);
```

### Ignore remaining parts of a value with `..`

```rust
struct Point {
		x: i32,
		y: i32,
		z: i32,
}

let origin = Point { x: 0, y: 0, z: 0 };

match origin {
		Point { x, .. } => println!("x is {}", x),
}
```

Using `..` must be unambiguous.

### `@` bindings

```rust
enum Message {
		Hello { id: i32 },
}

let msg = Message::Hello { id: 5 };

match msg {
		Message::Hello {
				id: id_variable @ 3..=7,
		} => println!("Found an id in range: {}", id_variable),
		Message::Hello { id: 10..=12 } => {
				println!("Found an id in another range")
		}  // comma optional when match arm expression is wrapped in {}
		Message::Hello { id } => println!("Found some other id: {}", id),
}
```

### `unsafe` superpowers

* Dereference a raw pointer
* Call an unsafe function or method
* Access or modify a mutable static variable
* Implement an unsafe trait
* Access fields of `union`s

`unsafe` does not turn off the borrow checker or disable any other of Rust's safety checks. You'll still get some degree of safety inside of an `unsafe` block.

### `*const T` and `*mut T` raw pointers

Immutable and mutable raw pointers, respectively. The asterisks are not the deference operator; it's part of the type name.

* allowed to ignore borrowing rules by having both immutable and mutable pointers or multiple mutable pointers to the same location
* are not guaranteed to point to valid memory
* are allowed to be null
* do not implement any automatic cleanup

Raw pointers can be created in safe code. Dereferencing raw pointers require the `unsafe` block.

### `extern`

Facilitates the creation and use of a FFI (foreign function interface). Functions defined in an `extern` block are always `unsafe`.

```rust
extern "C" {
    fn abs(input: i32) -> i32;
}
```

The `"C"` part defines which *application binary interface (ABI)* the external function uses: the ABI defines how to call the function at the assembly level. The `"C"` ABI is the most common and follows the C programming language’s ABI.

### calling rust functions from other languages

Use the `extern` keyword and specify the ABI to use. `#[no_mangle]` annotation is also needed.

```rust
#[no_mangle]
pub extern "C" fn call_from_c() {
    println!("Just called a Rust function from C!");
}
```

### static variables

Global variables using the `static` keyword in screaming snakecase. Static variables can only store references with the `'static` lifetime.

```rust
static HELLO_WORLD: &str = "Hello, world!";

fn main() {
    println!("name is: {}", HELLO_WORLD);
}
```

Constants are different in that they are allowed to duplicate data whenever they are used. Static variables can also be mutable which when accessing or modifying is considered `unsafe`.

```rust
static mut COUNTER: u32 = 0;

fn add_to_count(inc: u32) {
    unsafe {
        COUNTER += inc;
    }
}

fn main() {
    add_to_count(3);

    unsafe {
        println!("COUNTER: {}", COUNTER);
    }
}
```

### associated types

```rust
pub trait Iterator {
    type Item; // associated type

    fn next(&mut self) -> Option<Self::Item>;
}
```

With associated types, you do not need to annotate types because you cannot implement a trait on a type multiple times.

### default generic type parameters

```rust
trait Add<RHS=Self> {
    type Output;

    fn add(self, rhs: RHS) -> Self::Output;
}
```

`RHS=Self` syntax is called default type parameters. RHS is short for right hand side. It defaults to `Self` (adding with another of the same type), but it can be changed to allow adding with another of a different type.

```rust
use std::ops::Add;

struct Millimeters(u32);
struct Meters(u32);

impl Add<Meters> for Millimeters {
    type Output = Millimeters;

    fn add(self, other: Meters) -> Millimeters {
        Millimeters(self.0 + (other.0 * 1000))
    }
}
```

### fully qualified syntax for disambiguation: calling methods with the same name

```rust
trait Pilot {
    fn fly(&self);
}

trait Wizard {
    fn fly(&self);
}

struct Human;

impl Pilot for Human {
    fn fly(&self) {
        println!("calling fly from Pilot impl");
    }
}

impl Wizard for Human {
    fn fly(&self) {
        println!("calling fly from Wizard impl");
    }
}

impl Human {
    fn fly(&self) {
        println!("calling fly from Human impl");
    }
}
```

When calling `fly` on an instance of `Human`, it defaults to calling the method directly implemented on the type.

To call the `fly` methods from either the `Pilot` trait or the `Wizard` trait, we need to use more explicit syntax to specify which `fly` method we mean.

```rust
fn main() {
    let person = Human;
    Pilot::fly(&person);
    Wizard::fly(&person);
    person.fly();
}
```

For associated functions, it's a bit different

```rust
trait Animal {
    fn baby_name() -> String;
}

struct Dog;

impl Dog {
    fn baby_name() -> String {
        String::from("Spot")
    }
}

impl Animal for Dog {
    fn baby_name() -> String {
        String::from("puppy")
    }
}

fn main() {
    println!("A baby dog is called a {}", <Dog as Animal>::baby_name());
}
```

Fully qualified syntax definition

```rust
<Type as Trait>::function(receiver_if_method, next_arg, ...);
```

### supertraits

```rust
use std::fmt;

trait OutlinePrint: fmt::Display {
		// fns
}
```

`OutlinePrint` requires the `Display` trait. Methods that are defined in `Display` are then callable in any of the functions defined in `OutlinePrint`

### newtype pattern to implement external traits on external types

Newtype pattern is a term that originates from the Haskell programming language. Orphan rule states that a trait can be implemented on a type as long as either trait or type are local to the crate.

A wrapper tuple struct can be used to get around the orphan rule, but has a downside:

* all methods of wrapped type would need to delegate to the outer wrapper type.
  * a solution to this would be to implement the `Deref` trait on the outer wrapper type
  * if not all methods are needed, would need to manually create methods that delegate to the inner wrapped type

### `!` never type

An empty type that has no value.

```rust
fn bar() -> ! {
    // --snip--
}
```

The function `bar` returns never. Another word for this is diverging function.

```rust
let guess: u32 = match guess.trim().parse() {
		Ok(num) => num,
		Err(_) => continue,
};
```

`continue` has a `!` value. Expressions of type `!` can be coerced into any other type. In this case, Rust decides that the type for `guess` is `u32`.

`panic!` and `loop` also have type `!`.

### dynamically sized types and the `Sized` trait

`str` is a DST. Because strings have variable size, this is not possible:

```rust
let s1: str = "Hello there!";
```

`&str`, however, only stores the starting position and length of the slice, so it can determine the size at compile time. `&str` is twice the length of `usize`. It is possible to use pointers like `Box` and `Rc` for `str`.

```rust
Box<str>
Rc<str>
```

Every trait is a DST.

```rust
&dyn Trait
Box<dyn Trait>
Rc<dyn Trait>
```

Rust implicitly adds a bound on `Sized` to every generic function.

```rust
fn generic<T>(t: T) {
    // --snip--
}

// to

fn generic<T: Sized>(t: T) {
    // --snip--
}
```

Relax restriction to have a known size at compile time:

```rust
// t parameter is borrowed
fn generic<T: ?Sized>(t: &T) {
    // --snip--
}
```

A trait bound on `?Sized` is the opposite of a trait bound on `Sized`: we would read this as “`T` may or may not be `Sized`.” This syntax is only available for `Sized`, not any other traits.

### function pointers

Passing functions into functions. Functions coerce to the type `fn` (function pointer type, not `Fn` closure trait).

```rust
fn add_one(x: i32) -> i32 {
    x + 1
}

fn do_twice(f: fn(i32) -> i32, arg: i32) -> i32 {
    f(arg) + f(arg)
}

fn main() {
    let answer = do_twice(add_one, 5);

    println!("The answer is: {}", answer);
}
```

Function pointers implement all three closure traits: `Fn`, `FnMut`, `FnOnce`, so you can always pass a function pointer as an argument for a function that expects a closure. 

Best practice: write functions using a generic type and one of the closure traits so your functions can accept either functions or closures.

### exploiting initializer syntax on tuple struct, tuple-struct enum variants

These types use `()` as initializer syntax, which looks like a function call. The initializers are actually implemented as functions returning an instance that’s constructed from their arguments.

```rust
enum Status {
    Value(u32),
    Stop,
}

let statuses: Vec<Status> = (0u32..20).map(Status::Value).collect();
```

### returning closures

Need to wrap in a pointer, so the compiler can determine its size.

```rust
fn returns_closure() -> Box<dyn Fn(i32) -> i32> {
    Box::new(|x| x + 1)
}
```

### macros

Types of macros:

* Declarative (via `macro_rules!`)
* Procedural
  * Custom `#[derive]` macros that specify code added with the `derive` attribute used on structs and enums
  * Attribute-like macros that define custom attributes usable on any item
  * Function-like macros that look like function calls but operate on the tokens specified as their argument

```rust
use proc_macro;

// a support library for macro authors when defining new macros
```

`derive` macros only work on structs and enums.

```rust
// example of an attribute-like macro

#[route(GET, "/")]
fn index() {}

#[proc_macro_attribute]
pub fn route(attr: TokenStream, item: TokenStream) -> TokenStream {}
```

```rust
// example of a function-like macro

let sql = sql!(SELECT * FROM posts WHERE id=1);

#[proc_macro]
pub fn sql(input: TokenStream) -> TokenStream {}
```

### `FnOnce() -> T + Send + 'static` explanation

```rust
pub fn spawn<F, T>(f: F) -> JoinHandle<T>
    where
        F: FnOnce() -> T + Send + 'static,
        T: Send + 'static
```

`FnOnce` trait is needed for closure that will be called once. The `F` type parameter also has the trait bound `Send` and the lifetime bound `'static`, which are useful in our situation: we need `Send` to transfer the closure from one thread to another and `'static` because we don’t know how long the thread will take to execute.