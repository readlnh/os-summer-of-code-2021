# RefCell, Cell and Interior Mutability
Interior mutability is quite a confusing concept. It's a design pattern in Rust that allows us to mutate data even when there are immutable references to that data.

As we all know, Rust memory safety is based on the follwing rules:

- Having several immutable references (`&T`) to the object(alos know as **aliasing**).
- Haveing one mutable reference (`&mut T`) to the object(also known as **mutability**).

This is enforced by the Rust compiler. However, there are situations where this rules is not flexiable enough. Sometimes it is required to have multiple references to an object and yet mutate it.

Shareable mutable containers exist to permit mutability in a controlled manner, even in the presence of aliasing. Both `Cell<T>` and `RefCell<T>` allow doing this in single-threaded way. Notes they are **not thread safe**(because they don't implemnet `Sync`).

## Interior mutability
Values of `Cell<T>` and `RefCell<T>` types may be mautated through shared references(i.e. the common `&T` type), whereas most Rust types can only be mutated through unique(`&mut T`)references. We say `Cell<T>` and `RefCell<T>` provide *interior mutability*, in contrast with the typical Rust types that exhibit *inherited mutability*..

## Difference between `Cell<T>` and `RefCell<T>`
Cell types come in two flavors:

- `Cell<T>` implements interior mutability by moving valuse in and out of the `Cell<T>`.
- `RefCell<T>` use **references** instead of values.

`RefCell<T>` also obeys the borrow check rules, but it allows mutable borrows checked at **runtime**. That means you can mutate the value inside the `RefCell<T>` even when the `RefCell<T>` is immutable.

However, just like compile-time brorrowing rules, `RefCell<T>` let us have multiple immutable references or one mutable reference at any point in time. If we break the rules, the code would panic at runtime instead of getting a compile error. For example:

```rust
use std::cell::RefCell;

fn main() {
    let a = RefCell::new(5);

    let b = a.borrow_mut();
    let c = a.borrow(); // this causes a panic
}
```

## When to use interior mutability
Interior mutability is the last choice. In Rust we perfer common inherited mutability. However, there are still some cases that interior mutability might be appropriate, or even *must* be used, e.g.

- Introducing mutability 'inside' of something immutable(e.g. `Rc<RefCell<_>>`).
- Implementation details of logically-immutable methods(e.g. for not expose mutability in an API).
- Mutating implementations of `Clone`(e.g. `Rc`, `Arc`).

[more details](https://doc.rust-lang.org/std/cell/index.html)


