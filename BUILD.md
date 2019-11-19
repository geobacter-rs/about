
Sadly, due to the nature of the custom driver and how it is currently installed
over the unmodified Rust driver (to keep the set of patches I have to periodically
rebase on top of Rust `master` small), Geobacter has no prebuilt packages available. :(

# Building the Geobacter Rust Compiler

In a checkout of Geobacter, run: 
`cargo run --manifest-path rust-builder/Cargo.toml -- --target-dir ../geobacter-rust --rustup`.
This will automatically checkout the Geobacter Rust sources, setup a config with the specific
options we need, build vanilla Rust, install our custom drivers, and then setup `rustup` with
a local toolchain named `geobacter`.

That's it!
