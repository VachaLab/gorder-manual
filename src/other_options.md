# Other options

## Membrane normal

By default, `gorder` uses the z-axis as the membrane normal, assuming that the membrane is built in the xy-plane. If your membrane is built in a different plane, you can manually specify the membrane normal in the input YAML file:

```yaml
membrane_normal: x
```

In this example, the membrane normal is oriented along the x-axis, meaning that the membrane is built in the yz-plane.

## Min samples

By default, `gorder` calculates order parameters for all bonds for which it collects at least one sample. If you want to increase the number of required samples, you can specify the minimum number of samples manually in the input YAML file:

```yaml
min_samples: 10000
```

In this example, at least 10,000 samples are required for the bond to have a valid order parameter. If the number of samples is lower than this value, the order parameter will be NaN.

## Overwrite

By default, `gorder` does not overwrite output files. Before writing an output file (or creating an output directory for the order maps), any existing file (or directory) with the same name is backed up. You can change this behavior by specifying the overwrite option in the input YAML file:

```yaml
overwrite: true
```

In this example, no backups will be created, and files will be overwritten directly.

You can also specify this option on the command line when running the `gorder` program:

```bash
$ gorder INPUT_YAML --overwrite
```

## Silent

By default, gorder writes a lot of information about the progress of the analysis to the terminal (standard output). If you prefer not to see any of this information, you can specify it in the input YAML file:

```yaml
silent: true
```

In this example, gorder will not write anything to standard output. Output files will still be generated, and the analysis will proceed as normal. If an error occurs, information about the error will be written to standard error output.

You can also specify this option on the command line when calling `gorder`:

```bash
$ gorder INPUT_YAML --silent
```