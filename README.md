![Rust](https://github.com/SimonTate/ubus-rs/actions/workflows/rust.yml/badge.svg)

ubus (Rust)
===========

**Work in progress**

This is a pure Rust library for implementing [OpenWRT ubus](https://openwrt.org/docs/techref/ubus) clients.

Goals
-----

* Minimal bloat
* Few dependencies
* Zero allocations inside main code
* `no_std` where possible
* Don't panic!

Supported
---------

* Unix-Domain-Socket + Type-Length-Value protocol support
* `blob` TLV format support
* High-level abstraction for `lookup` command

TODO
----

* High level abstraction for `call` command
* High level abstraction for `subscribe`/`unsubscribe` commands
* High level support for network interface objects
* HTTP(S) + JSON protocol support

Getting Started
---------------

### Local Execution

On OpenWrt version 24.10 and later, you can use OpenSSH's UNIX domain socket forwarding feature to execute code on your development machine:

    ssh root@openwrtrouter -L /path/to/local/ubus.sock:/var/run/ubus/ubus.sock

You will either need to use `/path/to/local/ubus.sock` in your code, or create a suitable symbolic link.

### Cross Compilation

Cross-compilation is relatively straight-forward unless your project depends on external C libraries.

Until OpenWrt gains full Rust support, to cross-compile for your intended router, install a suitable rust target in your development environment.  Which one is needed depends on your target's CPU (discoverable with `uname -m`), e.g. for `aarch64`, you can use `rustup target install aarch64-unknown-linux-musl`.

    CARGO_TARGET_AARCH64_UNKNOWN_LINUX_MUSL_LINKER=rust-lld cargo build --release --target aarch64-unknown-linux-musl --example lookup

…will build a binary in:

    ./target/aarch64-unknown-linux-musl/release/examples/lookup

Alternatively you may use an OpenWrt build directory for the tools e.g.

    RUSTFLAGS='-L /path/to/openwrt/staging_dir/toolchain-mips_24kc_gcc-13.3.0_musl/lib/gcc/mips-openwrt-linux-musl/13.3.0/' \
    CARGO_TARGET_MIPS_UNKNOWN_LINUX_MUSL_LINKER=/path/to/openwrt/staging_dir/toolchain-mips_24kc_gcc-13.3.0_musl/mips-openwrt-linux-musl/bin/ld \
    cargo +nightly -Zbuild-std build --release --target mips-unknown-linux-musl --example lookup
