## 读取有不同分隔符的 CSV 记录

<!--
> [encoding/csv/delimiter.md](https://github.com/rust-lang-nursery/rust-cookbook/blob/master/src/encoding/csv/delimiter.md)
> <br />
> commit b61c8e588ad8445de36cd5f28e99232b5f858a41 - 2020.06.01
-->

[![csv-badge]][csv] [![cat-encoding-badge]][cat-encoding]

使用制表（tab）分隔符 [`delimiter`] 读取 CSV 记录。

```rust,edition2018
use csv::Error;
use serde::Deserialize;
#[derive(Debug, Deserialize)]
struct Record {
    name: String,
    place: String,
    #[serde(deserialize_with = "csv::invalid_option")]
    id: Option<u64>,
}

use csv::ReaderBuilder;

fn main() -> Result<(), Error> {
    let data = "name\tplace\tid
		Mark\tMelbourne\t46
		Ashley\tZurich\t92";

    let mut reader = ReaderBuilder::new().delimiter(b'\t').from_reader(data.as_bytes());
    for result in reader.deserialize::<Record>() {
        println!("{:?}", result?);
    }

    Ok(())
}
```

[`delimiter`]: https://docs.rs/csv/1.0.0-beta.3/csv/struct.ReaderBuilder.html#method.delimiter
