
# All targets

First you'll need to create the `Context`:
```rust
extern crate legionella_runtime_core as lrt_core;
use lrt_core::context::Context;

# fn main() {
  let ctx = Context::new().expect("create context");
# }
```

# AMDGPU

First we have to create the AMDGPU device object:
```rust
extern crate legionella_runtime_amd  as lrt_amd;
extern crate legionella_runtime_core as lrt_core;

use lrt_amd::HsaAmdGpuAccel;
use lrt_core::context::Context;

# fn main() -> Result<(), Box<dyn Error>> {
  let ctx = Context::new()?;
  let accel = HsaAmdGpuAccel::first_device(&ctx)?;
# }
```

TODO write the rest of this you lazy bum.
