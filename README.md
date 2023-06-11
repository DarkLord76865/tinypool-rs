# tinypool-rs

***tinypool*** is a simple thread pool implementation in Rust.

A thread pool is a collection of threads that can be used to execute jobs concurrently.
This crate is mostly based on the threadpool implementation from the Rust book, with some additional features.
The aim of this crate is to provide a simple, easy-to-use thread pool that can be used in any project.

[**Documentation**](https://docs.rs/tinypool/latest/tinypool/ "docs.rs")

[**Crate**](https://crates.io/crates/tinypool "crates.io")

### Example (1)
```rust
use tinypool::ThreadPool;
use std::sync::{Arc, Mutex};

let mut threadpool = ThreadPool::new(4).unwrap();
let counter = Arc::new(Mutex::new(0));

for _ in 0..100 {
    let counter_thrd = Arc::clone(&counter);
    threadpool.execute(move || {
        let mut counter = counter_thrd.lock().unwrap();
        *counter += 1;
    }).unwrap();
}

threadpool.join();
assert_eq!(*counter.lock().unwrap(), 100);
```

### Example (2)
```rust
use tinypool::ThreadPool;
use std::sync::mpsc;

let mut threadpool = ThreadPool::new(4).unwrap();
let (tx, rx) = mpsc::channel();

for i in 0..100 {
    let tx = tx.clone();
    threadpool.execute(move || tx.send(i).unwrap()).unwrap();
}
drop(tx);

let mut sum = 0;
while let Ok(i) = rx.recv() {
   sum += i;
}

assert_eq!(sum, 4950);
```
