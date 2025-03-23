# Trajectory file formats

`gorder` is highly optimized to read XTC files as quickly as possible. If you have an XTC file or can generate one, it is recommended to use it. However, `gorder` also supports various other trajectory file formats, namely TRR, GRO, PDB, Amber NetCDF, DCD, and LAMMPSTRJ. You can use any of these files instead of an XTC trajectory, and `gorder` will automatically recognize the format based on the file extension.

> `gorder` identifies the file format based on the file extension: `.xtc` for XTC files, `.trr` for TRR files, `.gro` for GRO files, `.pdb` for PDB files, `.nc` for Amber NetCDF files, `.dcd` for DCD files, and `.lammpstrj` for LAMMPSTRJ files. Ensure that your trajectory file is named accordingly.

Note that some features of `gorder` are not available for certain trajectory file formats. Specifically, do not specify the analysis time range using `begin` and `end` if your trajectory file is a PDB file or an Amber NetCDF file. **This will not work and will result in an error!** 

You can only use this feature with a GRO trajectory if the file contains simulation time and step information in the 'title' line, formatted as follows:

```text
Some Arbitrarily Long Title (...) t= SIMULATION_TIME step= SIMULATION_STEP
```

For example:

```text
System t= 100.00000 step= 5000
```

Additionally, note that `gorder` assumes that time information in DCD files is specified in `ps`.