# Trajectory file formats

`gorder` is highly optimized to read XTC files as quickly as possible. If you have an XTC file or can generate one, it is recommended to use it. However, `gorder` also supports TRR and GRO trajectory file formats. You can use any of these files instead of an XTC trajectory, and `gorder` will automatically recognize the format based on the file extension.

> `gorder` identifies the file format based on the file extension: `.xtc` for XTC files, `.trr` for TRR files and `.gro` for GRO files. Ensure that your trajectory file is named accordingly.

Note that if you want to specify the analysis time range using `begin` and `end` while providing a GRO trajectory, the file must contain simulation time and step information in the 'title' line, formatted as follows:

```text
Some Arbitrarily Long Title (...) t= SIMULATION_TIME step= SIMULATION_STEP
```

For example:

```text
System t= 100.00000 step= 5000
```