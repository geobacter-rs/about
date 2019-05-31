# The Legionella Rust Compiler and Runtime

This repository houses info about Legionella; how to use it, how all these
repositories fit together, and how Legionella (ab)uses the Rust compiler to
support its magic.

## What is Legionella?

Legionella is a framework to support single source accelerator programming,
without requiring compiling from source twice. However, Legionella is *not* a
JIT; the expectation is that kernels are invoked repeatedly and are possibly
also expensive to run.

For the time being, the focus is on the Vulkan API and its SPIR-V flavor. In the
future, the hope is support things like writing and running eBPF programs inside
the Linux kernel and general distributed compute.

In the past, AMDGPU/HSA was the target (and funily enough, this was much easier
to support than Vulkan/SPIRV is turning out to be); but this was dropped to pave
the way to using a cross platform/hardware API, Vulkan.

## How does this work?

In the Rust type system, every `fn` is given a unique type. Thus, we can refer
to a function definition just by its type. In our Rust fork, we use special
driver-defined intrinsic functions to essentially return a form of the Rust
compiler's `DefId`, along with other info to support SPIR-V pipeline
descriptions.

At runtime, we respool the compiler driver (into the runtime driver) using
metadata from every dependency. We then lookup the real `DefId` and, through
some compiler provider API tricks, get the LLVM codegen crate to generate IR for
the corresponding function and all of its dependencies. To be clear though, this
is not a JIT, and is ill-suited to JIT-esk things.

There is an in process cache, in the form of compiler generated statics, so
kernels aren't&ndash;generally&ndash; codegenned more than once per
accelerator.

## Status (ie what does and doesn't work)

Generally, everything up to LLVM optimizations works. Getting an id for a
specific function at compile time works. At runtime, loading every crate's
metadata and using that to set up a pseudo Rust driver works, as well taking the
aforementioned function id and running codegen for it&ndash;including optimizations and
(if applicable) sending it to a target machine&ndash;a second time works.

What doesn't work is pretty much everything after that.


### Vulkan/SPIRV

Legionella supports (or will, once a proc-macro gets written help with this)
single definition descriptor set/binding numbers; that is, both the resulting
SPIR-V global and the host code will use the same numbers when they refer to the
global or binding type, respectively.

The hope is to also allow creating entire graphics pipelines; ie vertex,
geometry, tess (eval and control), raytracing, and fragment "kernels", which are
then codgenned into a single SPIR-V module.


## How do I code with this?

See CODING.md! :^)

## How to build?

See BUILD.md.

## Who is working on this?

New contributors are absolutely welcome.

For now, I, Richard Diamond, am the sole developer working on this, which I do
in my free time. I am available for hire; my email is
`wichard`(at)`vitalitystudios.com` (discussion also welcome).
