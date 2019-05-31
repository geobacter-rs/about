
Sadly, due to the nature of the custom driver and how it is currently installed
over the unmodified Rust driver, Legionella has no prebuilt packages
available. :(

# Building the Legionella Rust Compiler

You'll need to compile
[this Rust fork](https://github.com/legionella-rs/rust) on the
`merge-head` branch. You'll need to use a specialized `config.toml`
for the build; the merger repo has an example `config.toml` in its
`target` directory
([link](https://github.com/legionella-rs/rust-merger/blob/master/target/config.toml)).
Then link into `rustup`:
` rustup toolchain link legionella $(pwd)/build/${HOST_TARGET}/stage2`
Next, override the default toolchain for this repo's directory:
`rustup override set legionella`, and run `./install-driver.sh`.

That's it!
