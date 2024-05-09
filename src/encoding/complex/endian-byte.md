## 以小端模式（低位模式）字节顺序读写整数

<!--
> [encoding/complex/endian-byte.md](https://github.com/rust-lang-nursery/rust-cookbook/blob/master/src/encoding/complex/endian-byte.md)
> <br />
> commit b61c8e588ad8445de36cd5f28e99232b5f858a41 - 2020.06.01
-->

[![byteorder-badge]][byteorder] [![cat-encoding-badge]][cat-encoding]

字节序 `byteorder` 可以反转结构化数据的有效字节。当通过网络接收信息时，这可能是必要的，例如接收到的字节来自另一个系统。

```rust,edition2018

use byteorder::{LittleEndian, ReadBytesExt, WriteBytesExt};
use std::io::Error;

#[derive(Default, PartialEq, Debug)]
struct Payload {
    kind: u8,
    value: u16,
}

fn main() -> Result<(), Error> {
    let original_payload = Payload::default();
    let encoded_bytes = encode(&original_payload)?;
    let decoded_payload = decode(&encoded_bytes)?;
    assert_eq!(original_payload, decoded_payload);
    Ok(())
}

fn encode(payload: &Payload) -> Result<Vec<u8>, Error> {
    let mut bytes = vec![];
    bytes.write_u8(payload.kind)?;
    bytes.write_u16::<LittleEndian>(payload.value)?;
    Ok(bytes)
}

fn decode(mut bytes: &[u8]) -> Result<Payload, Error> {
    let payload = Payload {
        kind: bytes.read_u8()?,
        value: bytes.read_u16::<LittleEndian>()?,
    };
    Ok(payload)
}
```
