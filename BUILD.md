
Sadly, due to the nature of the custom driver and how it is currently installed
over the unmodified Rust driver (to keep the set of patches I have to periodically
rebase on top of Rust `master`), Legionella has no prebuilt packages available. :(

# Building the Legionella Rust Compiler

In a checkout of Legionella, run: 
`cargo run --manifest-path rust-builder/Cargo.toml -- --target-dir ../legionella-rust --rustup`.
This will automatically checkout the Legionella Rust sources, setup a config with the specific
options we need, build vanilla Rust, install our custom drivers, and then setup `rustup` with
a local toolchain named `legionella`.

That's it!
