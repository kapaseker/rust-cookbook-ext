## Int 与 u8  

安全转换  

```rust

if let Ok(b) = i8::try_from(18) {
    println!("b: {}", b);        
}

if let Err(_) = i8::try_from(1888) {
    println!("not safe");        
}

```

不安全转换  

```rust
let a:i32 = 10099;
let b:u8 = a as u8;
println!("b: {}", b);
```