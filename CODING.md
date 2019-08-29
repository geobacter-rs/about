
# All targets

First you'll need to create the `Context`:
```rust
extern crate geobacter_runtime_core as grt_core;
use grt_core::context::Context;

# fn main() {
  let ctx = Context::new().expect("create context");
# }
```

# AMDGPU

First we have to create the AMDGPU device object:
```rust
extern crate geobacter_runtime_amd  as grt_amd;
extern crate geobacter_runtime_core as grt_core;

use grt_amd::HsaAmdGpuAccel;
use grt_core::context::Context;

# fn main() -> Result<(), Box<dyn Error>> {
  let ctx = Context::new()?;
  let accel = HsaAmdGpuAccel::first_device(&ctx)?;
# }
```

TODO write the rest of this you lazy bum.
