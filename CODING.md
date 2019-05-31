# Coding Guide

TODO At this point in the project, other parts of Legionella need to be inplace
and working in order for a end developer coding guide to actually be useful.

However, there are a few examples in the main Legionella repository which whould
help guide for the time being.

## Writing kernels

Essentially, accerelerators take a function and run it in lockstep over many
processing units. Now, to Rust code, this is fundamental soundness violation, as
mutable borrows are no longer unique! Whats worse is that the SIMD-y nature of a
workgroup (that is, multiple units of execution on a shared instruction pointer)
is hidden from the compiler. So Rust has no idea that a `&mut [u8]` function
argument isn't unique to the current "thread". Legionella's solution to this
issue is:

* Legionella kernels must take no arguments and must return `()` (ie no return
  value).

(Also required by Vulkan SPIRV). This enforces the use of globals to get data in
and out of shaders/kernels. For Rust code, this is also good: `static mut` is
required, which requires unsafe blocks to use, reflecting the nature of the
shared mutable borrow.

Note, these global aren't useful at all on the host (not to mention their use is
multithread unsafe!). Their host-side use doesn't currently cause an error
(TODO), but they can't be removed with `#[cfg(..)]` because failing cfg
attributes are always removed before the compiler stages which Legionella
operates.

### Legionella attributes

TODO unstable ATM; need to finish figuring out what works and then cleanup the
result.
