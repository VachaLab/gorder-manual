# Getting all analysis options

When `gorder` runs, it automatically sets many analysis options to their default values if they are not explicitly specified. To view the full set of parameters used in an analysis, including defaults, you can run the `gorder` program with the `--export-config` argument. For example:

```
$ gorder analyze.yaml --export-config analyze_out.yaml
```

The file `analyze_out.yaml` will contain all the input parameters for the analysis, including:
 - parameters specified in the input configuration file,
 - parameters provided on the command line, and
 - default values filled in by `gorder`.

This output file can also be used as a valid input configuration file for subsequent `gorder` runs.