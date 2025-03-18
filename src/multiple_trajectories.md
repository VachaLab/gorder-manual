# Providing multiple trajectory files

If the trajectory you are analyzing is split across multiple files, you can provide all of them without having to manually merge them into a single file.

To do this, specify all your trajectory files as a list in your configuration YAML file:
```yaml
structure: system.tpr
trajectory: [md1.xtc, md2.xtc, md3.xtc, md4.xtc, md5.xtc]
analysis_type: !AAOrder
  heavy_atoms: "@membrane and name r'C3.+|C2.+'"
  hydrogens: "@membrane and element name hydrogen"
output: order.yaml
```

`gorder` will seamlessly concatenate all the trajectory files and analyze them. If the individual trajectories are from the same simulation and follow each other directly, the last frame of a previous trajectory file will match the first frame of the subsequent file. Don't worry, `gorder` will automatically detect this scenario and consider only one of these frames. In contrast, if the frames have different simulation times, both will be included in the analysis. Note that if other frames in the trajectory share the same simulation time, `gorder` will analyze all of them. The tool only filters out duplicate frames at the boundaries between individual files.

> Trajectory concatenation is supported **only for XTC and TRR** files. All files must be of the same trajectory type; concatenating XTC files with TRR files is not supported.

Alternatively, instead of listing all trajectory files, you can specify them using a glob pattern:
```yaml
structure: system.tpr
trajectory: md*.xtc
analysis_type: !AAOrder
  heavy_atoms: "@membrane and name r'C3.+|C2.+'"
  hydrogens: "@membrane and element name hydrogen"
output: order.yaml
```

In this case, `gorder` will read all XTC files in the current directory whose names start with `"md"`, such as `md1.xtc`, `md2.xtc`, `md3.xtc`, as well as `mdXYZ.xtc`, `md239474.xtc`, and similar files, if they exist.

> Glob returns files in lexicographic order based on filenames. As a result, `md10.xtc` may appear before `md2.xtc`. Always verify the order of XTC files and ensure that filenames are structured so that lexicographic and numerical ordering align. For example, when dealing with trajectories 1–99, use filenames like `md01.xtc` to `md99.xtc` to maintain the correct order.