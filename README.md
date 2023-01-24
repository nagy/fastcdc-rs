# FastCDC [![docs.rs](https://docs.rs/fastcdc/badge.svg)](https://docs.rs/fastcdc) [![Crates.io](https://img.shields.io/crates/v/fastcdc.svg)](https://crates.io/crates/fastcdc) ![Test](https://github.com/nlfiedler/fastcdc-rs/workflows/Test/badge.svg)

This crate contains multiple implementations of the "FastCDC" content defined chunking algorithm described in 2016 by Wen Xia, et al. A critical aspect of its behavior is that it returns exactly the same results for the same input. To learn more about content defined chunking and its applications, see the reference material linked below.

## Requirements

* [Rust](https://www.rust-lang.org) stable (2018 edition)

## Building and Testing

```shell
$ cargo clean
$ cargo build
$ cargo test
```

## Example Usage

An example can be found in the `examples` directory of the source repository,
which demonstrates reading files of arbitrary size into a memory-mapped buffer
and passing them through the chunker (and computing the SHA256 hash digest of
each chunk).

```shell
$ cargo run --example dedupe -- --size 16384 test/fixtures/SekienAkashita.jpg
    Finished dev [unoptimized + debuginfo] target(s) in 0.03s
     Running `target/debug/examples/dedupe --size 16384 test/fixtures/SekienAkashita.jpg`
hash=e63429b466becce0ec9eaa9e257f7065a0ba22e2e34f8c2fab4e284b46516400 offset=0 size=32768
hash=33f5615317b5672c281da3f55a357a3532799814f4248102f15abd7b8d178fb8 offset=32768 size=32768
hash=952bb9483c7c08d45c5f059b62277690a6ac7d6fcdb5a53b76fd4c937fbce3bd offset=65536 size=26610
hash=7fa5b12134dc75cd2ac8dc60d3a8f3c8d22f0ee9d4cf74a4aa937e2a0d2d79a5 offset=92146 size=17320
```

The unit tests also have some short examples of using the chunker, of which this
code snippet is an example:

```rust
let read_result = fs::read("test/fixtures/SekienAkashita.jpg");
assert!(read_result.is_ok());
let contents = read_result.unwrap();
let chunker = chunker::v2016::FastCDC::new(&contents, 16384, 32768, 65536);
let results: Vec<Chunk> = chunker.collect();
assert_eq!(results.len(), 2);
assert_eq!(results[0].offset, 0);
assert_eq!(results[0].length, 66549);
assert_eq!(results[1].offset, 66549);
assert_eq!(results[1].length, 42917);
```

## Reference Material

The algorithm is as described in "FastCDC: a Fast and Efficient Content-Defined Chunking Approach for Data Deduplication"; see the [paper](https://www.usenix.org/system/files/conference/atc16/atc16-paper-xia.pdf), and [presentation](https://www.usenix.org/sites/default/files/conference/protected-files/atc16_slides_xia.pdf) for details. There are some minor differences, as described below.

## Other Implementations

* [jrobhoward/quickcdc](https://github.com/jrobhoward/quickcdc)
    + Similar but slightly earlier algorithm by some of the same researchers?
* [rdedup_cdc at docs.rs](https://docs.rs/crate/rdedup-cdc/0.1.0/source/src/fastcdc.rs)
    + Alternative implementation in Rust.
* [ronomon/deduplication](https://github.com/ronomon/deduplication)
    + C++ and JavaScript implementation of a variation of FastCDC.
* [titusz/fastcdc-py](https://github.com/titusz/fastcdc-py)
    + Pure Python port of FastCDC. Compatible with this implementation.
* [wxiacode/destor](https://github.com/wxiacode/destor/blob/master/src/chunking)
    + Canonical algorithm in C with gear table generation and mask values.
* [wxiacode/restic-FastCDC](https://github.com/wxiacode/restic-FastCDC)
    + Alternative implementation in Go with additional mask values.
