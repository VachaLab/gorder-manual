# Using `gorder` as a Rust crate

`gorder` can also be used as a Rust crate, allowing you to call it directly from your Rust code.

To use `gorder` in your project, first add it as a dependency:

```bash
$ cargo add gorder
```

Next, include the crate into your Rust code:

```rust
use gorder::prelude::*;
```

Once imported, you can access all the options and functionality described in this manual. 

For instance, the following input YAML file:

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !CGOrder
  beads: "@membrane"
output: order.yaml
```

can also by written as the following Rust code:

```rust
let analysis = Analysis::builder()
    .structure("system.tpr")
    .trajectory("md.xtc")
    .analysis_type(AnalysisType::cgorder("@membrane"))
    .output("order.yaml")
    .build()?;
```

You can then run the analysis like this:
```rust
let results = analysis.run()?;
```

and either write the results into the output file(s):

```rust
results.write()?;
```

or access them programmatically.

See the Rust API documentation on [docs.rs](https://docs.rs/gorder/latest/gorder) for more information about using `gorder` as a Rust crate.