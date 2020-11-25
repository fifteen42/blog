---
title: "为什么用 assert_eq 比较 string 和 &str 会成功？"
date: 2020-11-20T15:11:30+08:00
draft: false
tags: ['rust']
---

昨天写代码的时候发现用 assert_eq 比较 string 和 &str 会成功，如下：

```Rust
let s1 = String::from("hello");
let s2 = "hello";
assert_eq!(s1, s2);
```

凭直觉来讲，这不应该能通过，因为 string 和 &str 是两种类型，然后我觉得是 assert_eq 这个宏做了特殊处理，于是我又试了一下

```Rust
let s1 = String::from("hello");
let s2 = "hello";
assert!(s1 == s2);
```

发现也没有问题，这说明和 assert_eq 没啥关系，于是我在 reddit 以及 Rust 中文社区提问，一个老哥的[回答](https://www.reddit.com/r/rust/comments/jwz1ae/why_use_assert_eq_to_compare_string_and_str_will/gct91pb?utm_source=share&utm_medium=web2x&context=3)是：

> String and &str cannot be compared at all, so Rust will look for ways to make the comparison possible. It will find that s1.deref() returns &str which obviously can be compared to &str (because Strings implement Deref<Target=str>) so it will automatically use that. This is called "deref coercion".

简言之，他的理解是因为强制解引用多态（deref coercion）使得 s1 自动调用 deref() ，而其实现了 Deref `<Target=str>`，所以返回 &str , 然后就能与 s2 相比较了。逻辑非常清晰，我都已经道谢了，但是后面证实这是不正确的，另一位[老哥](https://github.com/gwy15)也对这种说法进行了严谨地反驳：

> 这个说法是错的，跟 Deref 没有直接的关系，你可以这样写：

```Rust
// assert macro will print using debug format
#[derive(Debug)]
struct S {
    inner: String,
}

impl S {
    pub fn new(inner: &str) -> Self {
        Self {
            inner: inner.to_owned(),
        }
    }
}

impl std::cmp::PartialEq<&str> for S {
    fn eq(&self, other: &&str) -> bool {
        return self.inner.eq(other);
    }
}

// comment for compile error
impl std::cmp::PartialEq<S> for &str {
    fn eq(&self, _other: &S) -> bool {
        return false;
    }
}

fn main() {
    let s = "hello";
    let string = String::from("hello");
    let fake_string = S::new("hello");
    assert_eq!(string, s);
    assert_eq!(fake_string, s);
    assert_eq!(s, fake_string);
}
```

> 这里 S 没有实现任何 deref，但是还是可以通过编译。把 `assert_eq` 宏展开之后是这样的：

```Rust
match (&s, &fake_string) {
            (left_val, right_val) => {
                if !(*left_val == *right_val) {
                    {
                        ::std::rt::begin_panic_fmt...
```

> 也就是说这里只调用了 lhs == rhs，只要实现了 PartialEq for Lhs 就可以了。 String 的文档里面两种顺序都实现了。

后面通过翻[文档](https://doc.rust-lang.org/std/string/struct.String.html#impl-PartialEq%3C%26%27a%20str%3E)发现 String 和 str 确实互相实现了 [PartialEq Trait](https://doc.rust-lang.org/std/cmp/trait.PartialEq.html)，所以 String 和 &str 是可以直接比较的。

感谢 rustaceans 的热心解答。