### 集中趋势度量

<!--
> [science/mathematics/statistics/central-tendency.md](https://github.com/rust-lang-nursery/rust-cookbook/blob/master/src/science/mathematics/statistics/central-tendency.md)
> <br />
> commit b61c8e588ad8445de36cd5f28e99232b5f858a41 - 2020.06.01
-->

[![std-badge]][std] [![cat-science-badge]][cat-science]

本节实例计算 Rust 数组中包含的数据集的集中趋势度量。对于一个空的数据集，可能没有平均数、中位数或众数去计算，因此每个函数都返回 [`Option`] ，由调用者处理。

第一个实例是通过对数据引用生成一个迭代器，然后计算平均数（所有测量值的总和除以测量值的计数），并使用 [`sum`] 和 [`len`] 函数分别确定值的总和及值的计数。

```rust,edition2018
fn main() {
    let data = [3, 1, 6, 1, 5, 8, 1, 8, 10, 11];

    let sum = data.iter().sum::<i32>() as f32;
    let count = data.len();

    let mean = match count {
       positive if positive > 0 => Some(sum  / count as f32),
       _ => None
    };

    println!("Mean of the data is {:?}", mean);
}
```

第二个实例使用快速选择算法（quick select algorithm）计算中位数，该算法只对已知可能包含中位数的数据集的分区进行排序，从而避免了完整[排序][`sort`]。该算法使用 [`cmp`] 和 [`Ordering`] 简便地地决定要检查的下一个分区，并使用 [`split_at`] 为每个步骤的下一个分区选择一个任意的枢轴量。

```rust,edition2018
use std::cmp::Ordering;

fn partition(data: &[i32]) -> Option<(Vec<i32>, i32, Vec<i32>)> {
    match data.len() {
        0 => None,
        _ => {
            let (pivot_slice, tail) = data.split_at(1);
            let pivot = pivot_slice[0];
            let (left, right) = tail.iter()
                .fold((vec![], vec![]), |mut splits, next| {
                    {
                        let (ref mut left, ref mut right) = &mut splits;
                        if next < &pivot {
                            left.push(*next);
                        } else {
                            right.push(*next);
                        }
                    }
                    splits
                });

            Some((left, pivot, right))
        }
    }
}

fn select(data: &[i32], k: usize) -> Option<i32> {
    let part = partition(data);

    match part {
        None => None,
        Some((left, pivot, right)) => {
            let pivot_idx = left.len();

            match pivot_idx.cmp(&k) {
                Ordering::Equal => Some(pivot),
                Ordering::Greater => select(&left, k),
                Ordering::Less => select(&right, k - (pivot_idx + 1)),
            }
        },
    }
}

fn median(data: &[i32]) -> Option<f32> {
    let size = data.len();

    match size {
        even if even % 2 == 0 => {
            let fst_med = select(data, (even / 2) - 1);
            let snd_med = select(data, even / 2);

            match (fst_med, snd_med) {
                (Some(fst), Some(snd)) => Some((fst + snd) as f32 / 2.0),
                _ => None
            }
        },
        odd => select(data, odd / 2).map(|x| x as f32)
    }
}

fn main() {
    let data = [3, 1, 6, 1, 5, 8, 1, 8, 10, 11];

    let part = partition(&data);
    println!("Partition is {:?}", part);

    let sel = select(&data, 5);
    println!("Selection at ordered index {} is {:?}", 5, sel);

    let med = median(&data);
    println!("Median is {:?}", med);
}
```

最后一个实例使用可变的 [`HashMap`] 来计算众数，[`fold`] 和 [`entry`] API 用来从集合中收集每个不同整数的计数。[`HashMap`] 中最常见的值可以用 [`max_by_key`] 取得。

```rust,edition2018
use std::collections::HashMap;

fn main() {
    let data = [3, 1, 6, 1, 5, 8, 1, 8, 10, 11];

    let frequencies = data.iter().fold(HashMap::new(), |mut freqs, value| {
        *freqs.entry(value).or_insert(0) += 1;
        freqs
    });

    let mode = frequencies
        .into_iter()
        .max_by_key(|&(_, count)| count)
        .map(|(value, _)| *value);

    println!("Mode of the data is {:?}", mode);
}
```

[Option]: https://doc.rust-lang.org/std/option/enum.Option.html
[sum]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.sum
[len]: https://doc.rust-lang.org/std/primitive.slice.html#method.len
[sort]: https://doc.rust-lang.org/std/primitive.slice.html#method.sort
[cmp]: https://doc.rust-lang.org/beta/std/cmp/trait.Ord.html#tymethod.cmp
[Ordering]: https://doc.rust-lang.org/beta/std/cmp/enum.Ordering.html
[split_at]: https://doc.rust-lang.org/std/primitive.slice.html#method.split_at
[HashMap]: https://doc.rust-lang.org/std/collections/struct.HashMap.html
[fold]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.fold
[entry]: https://doc.rust-lang.org/std/collections/hash_map/enum.Entry.html
[max_by_key]: https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.max_by_key
