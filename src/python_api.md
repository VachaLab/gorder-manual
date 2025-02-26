# Using `gorder` as a Python package

`gorder` can also be used as a Python package, allowing you to call it directly from your Python code.

To install `gorder`, you can use `pip`:

```bash
$ pip install git+https://github.com/Ladme/gorder.git#subdirectory=python
```

However, you **should** use the [uv](https://github.com/astral-sh/uv) package manager instead. To add `gorder` to your `uv` project, run:

```bash
$ uv add git+https://github.com/Ladme/gorder.git#subdirectory=python
```

Next, import the package into your Python code:

```python
import gorder
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

can also by written as the following Python code:

```python
analysis = gorder.Analysis(
    structure = "system.tpr",
    trajectory = "md.xtc",
    analysis_type = gorder.analysis_types.CGOrder("@membrane"),
    output_yaml = "order.yaml",
)
```

You can then run the analysis like this:
```python
results = analysis.run()
```

and either write the results into the output file(s):

```python
results.write()
```

or access them programmatically.

See the Python API documentation on [TODO: insert link](????) for more information about using `gorder` as a Python package.