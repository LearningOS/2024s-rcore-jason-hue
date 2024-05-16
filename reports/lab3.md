### Q1
理论上，下一个应该是步幅最小的进程执行。
p1.stride = 255
p2.stride = 4
显然，4 < 255，所以应该轮到 p2 执行。

实际上，由于溢出，轮到p1 执行
### Q2


### Q3
```rust
use core::cmp::Ordering;

struct Stride(u64);

impl PartialOrd for Stride {
    fn partial_cmp(&self, other: &Self) -> Option<Ordering> {
        // Assuming BigStride is the maximum value for 8-bit storage, which is 255
        const BIG_STRIDE: u64 = 255;

        let self_val = self.0 as i64;
        let other_val = other.0 as i64;

        // Calculate the difference considering 8-bit overflow
        let diff = self_val - other_val;

        if diff.abs() > (BIG_STRIDE / 2) as i64 {
            // If the absolute difference is greater than half of BigStride, handle overflow
            if self_val > other_val {
                Some(Ordering::Less)
            } else {
                Some(Ordering::Greater)
            }
        } else {
            // Normal comparison
            self_val.partial_cmp(&other_val)
        }
    }
}

impl PartialEq for Stride {
    fn eq(&self, other: &Self) -> bool {
        false
    }
}

fn main() {
    // Example usage
    let p1_stride = Stride(255);
    let p2_stride = Stride(4);

    // Compare strides
    if p1_stride < p2_stride {
        println!("p1 should run next.");
    } else {
        println!("p2 should run next.");
    }

    // Test with BinaryHeap
    use std::collections::BinaryHeap;

    let mut heap = BinaryHeap::new();
    heap.push(Stride(255));
    heap.push(Stride(4));

    // Pop the smallest stride
    if let Some(smallest_stride) = heap.pop() {
        println!("Smallest stride: {}", smallest_stride.0);
    }
}

```