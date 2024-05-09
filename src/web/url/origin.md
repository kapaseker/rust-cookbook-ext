## 提取 URL 源（scheme / host / port）

<!--
> [web/url/origin.md](https://github.com/rust-lang-nursery/rust-cookbook/blob/master/src/web/url/origin.md)
> <br />
> commit b61c8e588ad8445de36cd5f28e99232b5f858a41 - 2020.06.01
-->

[![url-badge]][url] [![cat-net-badge]][cat-net]

[`Url`] 结构体定义了多种方法，以便于提取有关它所表示的 URL 的信息。

```rust,edition2018

use url::{Url, Host, ParseError};

fn main() -> Result<(), ParseError> {
    let s = "ftp://rust-lang.org/examples";

    let url = Url::parse(s)?;

    assert_eq!(url.scheme(), "ftp");
    assert_eq!(url.host(), Some(Host::Domain("rust-lang.org")));
    assert_eq!(url.port_or_known_default(), Some(21));
    println!("The origin is as expected!");

    Ok(())
}
```

[`origin`] 方法产生相同的结果。

```rust,edition2018
# use error_chain::error_chain;

use url::{Url, Origin, Host};

# error_chain! {
#     foreign_links {
#         UrlParse(url::ParseError);
#     }
# }
#
fn main() -> Result<()> {
    let s = "ftp://rust-lang.org/examples";

    let url = Url::parse(s)?;

    let expected_scheme = "ftp".to_owned();
    let expected_host = Host::Domain("rust-lang.org".to_owned());
    let expected_port = 21;
    let expected = Origin::Tuple(expected_scheme, expected_host, expected_port);

    let origin = url.origin();
    assert_eq!(origin, expected);
    println!("The origin is as expected!");

    Ok(())
}
```

[`origin`]: https://docs.rs/url/*/url/struct.Url.html#method.origin
[`Url`]: https://docs.rs/url/*/url/struct.Url.html
