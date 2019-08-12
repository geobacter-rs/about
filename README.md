# The Legionella Rust Compiler and Runtime :construction:

This repository houses info about Legionella; how to use it, how all these
repositories fit together, and how Legionella (ab)uses the Rust compiler to
support its magic.

## What is Legionella?

Legionella is a framework to support single source accelerator programming,
without requiring compiling from source twice. However, Legionella is *not* a
JIT; the expectation is that kernels are invoked repeatedly and are possibly
also expensive to run. In fact, due to the nature of single function kernels,
Legionella enables a number of LLVM options which would too costly to run for
all crates.

Currently, AMDGPU and HPC is the focus. In the future, support for generating eBPF
functions will be added. Later, HLS will be supported.

## How does this work?

In the Rust type system, every `fn` is given a unique type. Thus, we can refer
to a function definition just by its type. In our Rust fork, we use special
driver-defined intrinsic functions to essentially return a form of the Rust
compiler's `ty::Instance<'_>`, along with other info specific to the desired
target (for example, to support SPIR-V pipeline descriptions)

At runtime, we respool the compiler driver (into the runtime driver) using
metadata from every dependency. We then lookup the real `DefId` and, through
some compiler provider API tricks, get the LLVM codegen crate to generate IR for
the corresponding function and all of its dependencies. To be clear though, this
is not a JIT, and is ill-suited to JIT-esk things; for instance, we enable extra
LLVM optimizations, like Polly as well as increase the optimization search space.
Thus, while the optimizations are *relatively* fast, for the purposes of a JIT,
they are too slow.

There is an in process cache, in the form of compiler generated statics, so
kernels aren't codegenned more than once per accelerator target.

## Status (ie what does and doesn't work)

Generally, everything up to LLVM optimizations works for all targets. 
Getting an id for a specific function at compile time works. At runtime, loading
every crate's metadata and using that to set up a pseudo Rust driver works, as well 
taking the aforementioned function id and running codegen for it including
optimizations and (if applicable) sending it to a target machine a second time works.

### AMDGPU

This target works out of the box, though dispatch requires some unsafety as the
design is still settling down.

TODOs :construction:
* Device side signals (for finer grained resource management)
* Device side qeueus  (for device side enqueue)
Requires casting the signal/queue handle to a AMD specifc signal/queue structure, which
is probably internal impl detail. Must also implement the `hsa_signal_*` and `hsa_queue_*`
functions in Rust. See [hsaqs.cl](https://github.com/RadeonOpenCompute/ROCm-Device-Libs/blob/master/ockl/src/hsaqs.cl).

* Device side allocation.
* Texture/Image support. Currently only "raw" buffers are usable.

### Vulkan/SPIRV

This target isn't receiving any support ATM, and is currently broken. SPIRV and LLVM
aren't really ready for each other. Maybe someday!

<!--
Legionella supports (or will, once a proc-macro gets written help with this)
single definition descriptor set/binding numbers; that is, both the resulting
SPIR-V global and the host code will use the same numbers when they refer to the
global or binding type, respectively.

The hope is to also allow creating entire graphics pipelines; ie vertex,
geometry, tess (eval and control), raytracing, and fragment "kernels", which are
then codgenned into a single SPIR-V module.
-->

## How to get the toolchain?

ATM, we don't have prebuilt compilers for you to download, let alone the ability to download 
directly from `rustup`.

So you'll need to build the Rust toolchain yourself. See BUILD.md.

TODO :construction: offer prebuilt packages.

## How do I code with this?

See CODING.md! :^)

## Who is working on this?

Vitality Studios offers paid support to help you accelerate your applications!
We are available for hire; inquires should be made to `dick`(at)`vitalitystudios.com`.

New contributors are absolutely welcome.
