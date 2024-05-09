## String 与 str

```rust
let my_string: String = String::from("Hello");
let my_string_str: &str = my_string.as_str();

let my_str: &str = "World!";
let my_str_string: String = my_str.to_string();

println!("{}, {}", my_string_str, my_str_string);
```