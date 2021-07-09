# Error handing

Two main types:
- recoverable errors
- unrecoverable errors

## Unrecoverable errors
Rust use `panic!` macro to deal with the unrecoverable errors. When `panic!` macro executes, your program will print a failture message, unwind and clean up the stack, and then quit.

>**Unwinding the Stack or Aborting in Response to a Panic**
By default, when a panic occurs, the program starts unwinding, which means Rust walks back up the stack and cleans up the data from each function it encounters. But this walking back and cleanup is a lot of work. The alternative is to immediately abort, which ends the program without cleaning up. Memory that the program was using will then need to be cleaned up by the operating system. If in your project you need to make the resulting binary as small as possible, you can switch from unwinding to aborting upon a panic by adding panic = 'abort' to the appropriate [profile] sections in your Cargo.toml file. For example, if you want to abort on panic in release mode, add this:
```toml
[profile.release]
panic = 'abort'
```

## Recoverable errors

Rust use `Result` type to deal with recoverable errors. The `Reslut` enum is defined as having two variants `Ok` and `Err`:

```
enum Result<T, E> {
    Ok(T),
    Err(E),
}
```

The `T` and `E` are generic type parameters, `T` repersents the type of values that will be returned in a success case within the `Ok` variant, and `E` represents the type of error that will be returned in a failure case within the `Err` variant.

### Matching on Different Errors

two examples:

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt");

    let f = match f {
        Ok(file) => file,
        Err(error) => match error.kind() {
            ErrorKind::NotFound => match File::create("hello.txt") {
                Ok(fc) => fc,
                Err(e) => panic!("Problem creating the file: {:?}", e),
            },
            other_error => {
                panic!("Problem opening the file: {:?}", other_error)
            }
        },
    };
}
```
Or use `unwrap_or_else`

```rust
use std::fs::File;
use std::io::ErrorKind;

fn main() {
    let f = File::open("hello.txt").unwrap_or_else(|error| {
        if error.kind() == ErrorKind::NotFound {
            File::create("hello.txt").unwrap_or_else(|error| {
                panic!("Problem creating the file: {:?}", error);
            })
        } else {
            panic!("Problem opening the file: {:?}", error);
        }
    });
}
```

### `unwrap` and `expect`

- `unwrap`:
    - If the `Result` value is the `Ok` variant, `unwrap` will return the value inside the `Ok`. If the `Result` value is the `Err` variant, `unwrap` will call the `panic!` macro.

- `expect`
  - Similar to `unwrap`, but  we can choose `panic` error message ourselves.

### `?` operator for propagating errors
`?` is almost same as the what `match` do propgating errors. If the value of the `Result` is an `Ok`, the value inside the `Ok` will get returned from this expression, and the program will continue. If the value is an `Err`, the `Err` will be returned from the whole function as if we had used the return keyword so the error value gets propagated to the calling code. 

### `Box<dyn Error>`
The Box<dyn Error> type is called a trait object, which means "any kind of error".
