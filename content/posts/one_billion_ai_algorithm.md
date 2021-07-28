---
title: "价值一个亿的人工智能算法（Rust 版）"
date: 2021-03-05T17:47:53+08:00
draft: false
---

```Rust 
#![allow(unused)]
use std::io::stdin;

fn main() {
    loop {
        let mut s = String::new();
        stdin().read_line(&mut s)
            .expect("Failed to read line.");

        s = s.replace("吗", "");
        s = s.replace("我", "你");
        s = s.replace("?", "!");
        
        println!("艾瑞斯：{}", s);
    }
}
```

### 使用

```
你好
艾瑞斯：你好

今天天气好吗
艾瑞斯：今天天气好

我是世界上最美的人吗
艾瑞斯：你是世界上最美的人

天呐,我竟然是猪 
艾瑞斯：天呐,你竟然是猪

吃饭?
艾瑞斯：吃饭!
```

### 来源

https://github.com/ruanyf/weekly/issues/158


---

Just for fun 🤣

