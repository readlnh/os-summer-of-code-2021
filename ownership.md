# Ownership
The core concept fro Rust

## Ownership Rules

- Each value in Rust has a variable that's called its owner.
- There can only be one owner at a time.
- When the owner goes out of scope, the value will be dropped.

## Variable Scope
A scope is the range within a program for which an item is valid.

```rust
{                      // s is not valid here, it’s not yet declared
    let s = "hello";   // s is valid from this point forward

    // do stuff with s
}                      // this scope is now over, and s is no longer valid
```
## The String Type
`String` type is allocated on the heap and as such is able to store an amount of text that is **unknown to us at compile time**. So the `String` can be mutated but the literals cannot.

## Memory and Allocation
Rust's way: the memory is automatically returned once the variable that owns it goes out of scope.

```rust
{
    let s = String::from("hello");      // s is valid from this point forward

                                        // do stuff with s
}                                       // this scope is now over, and s is no
                                       // longer valid
```
### Ways Varibales and Data Inreract: Clone

- move (shadow copy)
- clone (deep copy)
- Stack-Only Data: Copy

## Ownership and functions

- Passing a value to a function will move or copy
- Returning value can also transfer ownership

## Reference rules for Rust
- At any given time, you can have *either* one mutable reference or any number of immutable references.
- References must always be valid.
