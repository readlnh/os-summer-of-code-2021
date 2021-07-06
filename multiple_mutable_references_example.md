# An interesting example about multiple mutable references
While doing the `rustlings` today, i met an interesting example:

```rust
fn main() {
    let mut a = 1;
    let b = &mut a;
    
    let c = &mut *b;
    
    *b = *b + 1; 
    *c = *c + 1;
    
    println!("{}", a);
}
```

The code is fail:

```shell
error[E0506]: cannot assign to `*b` because it is borrowed
 --> src/main.rs:7:5
  |
5 |     let c = &mut *b;
  |             ------- borrow of `*b` occurs here
6 |     
7 |     *b = *b + 1; 
  |     ^^^^^^^^^^^ assignment to borrowed `*b` occurs here
8 |     *c = *c + 1;
  |          -- borrow later used here

error[E0503]: cannot use `*b` because it was mutably borrowed
 --> src/main.rs:7:10
  |
5 |     let c = &mut *b;
  |             ------- borrow of `*b` occurs here
6 |     
7 |     *b = *b + 1; 
  |          ^^ use of borrowed `*b`
8 |     *c = *c + 1;
  |          -- borrow later used here

error: aborting due to 2 previous errors
```

This is because mutable references have one big restriction: you can have only one mutable reference to a particular piece of data in a particular scope.

We can change the order of the code like this:

```rust
fn main() {
    let mut a = 1;
    let b = &mut a;
    
    let c = &mut *b;

    *c = *c + 1;
    *b = *b + 1; 

    println!("{}", a);
}
```
This is ok, why? We can add some `{}` to make this code more clear.

```rust
fn main() {
    let mut a = 1;
    let b = &mut a;
    
    {                   
        let c = &mut *b;
        *c = *c + 1;
    } // here `c` goes out of scope
    
    *b = *b + 1;

    println!("{}", a);
}
```

So in this case, we can print variable `a` or `b` in the last line, however, we can't do it for `c`. If we try to do so, the `c`'s scope is the same as `b` that means we assign to `*b` when it is mutable borrowed which is not allowed in Rust.