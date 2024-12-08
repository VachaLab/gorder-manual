# Using `gorder` as a Rust crate

`gorder` can also be used as a Rust crate, allowing you to call it directly from your Rust code.

To use `gorder` in your project, first add it as a dependency:

```bash
$ cargo add gorder
```

Next, import the crate into your Rust code:

```rust
use gorder::prelude::*;
```

Once imported, you can access all the options and functionality described in this manual. 

For instance, the following input yaml file:

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !CGOrder
  beads: "@membrane"
output: order.yaml
```

can also by written as the following Rust code:

```rust
Analysis::new()
    .structure("system.tpr")
    .trajectory("md.xtc")
    .analysis_type(AnalysisType::cgorder("@membrane"))
    .output("order.yaml")
    .build()
    .unwrap()
```

For some more examples on how to specify the analysis options and how to perform the analysis in Rust, refer to the corresponding [docs.rs](https://docs.rs/gorder/latest/gorder).