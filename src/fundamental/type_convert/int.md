## String 与 int

```rust
let a     = "27".to_string().parse::<i32>().unwrap();
let b:i32 = "13".to_string().parse().unwrap();

println!("a + b: {}", a + b);

```