# Atomistic order parameters

## Preparing the input

Suppose we have a membrane composed of POPC, POPE, and POPG lipids.

To calculate atomistic order parameters, we need two Gromacs files:
- A TPR file containing the system structure and topology (`system.tpr`).
- An XTC trajectory file (`md.xtc`) whose frames will be analyzed.

Next, we create an input YAML file that specifies the options for the analysis:

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !AAOrder
  heavy_atoms: "@membrane and name r'C3([2-9]|1[0-6])|C2([2-9]|1[0-8])'"
  hydrogens: "@membrane and element name hydrogen"
output: order.yaml
```

In the input YAML file, the analysis type `AAOrder` requires you to specify both `heavy_atoms` and `hydrogens`. `gorder` will then identify all bonds connecting the selected heavy atoms with the selected hydrogen atoms. The order parameters are calculated for all these identified bonds.
  
The atoms are selected using a query language called [GSL](https://docs.rs/groan_rs/latest/groan_rs/#groan-selection-language). If you are familiar with the query language used in VMD, you'll find the basic syntax of GSL intuitive.

Here:
- `heavy_atoms` are selected using the query `@membrane and name r'C3([2-9]|1[0-6])|C2([2-9]|1[0-8])'`, which selects all palmitoyl and oleoyl carbons of the membrane lipids.
- `hydrogens` are selected using the query `@membrane and element name hydrogen`.

> `@membrane` is a GSL autodetection macro that selects all atoms of common membrane lipids. The block starting with `r'` is a regular expression which are also supported by GSL.

The results of the analysis will be saved in the `order.yaml` file.

## Running the analysis

We save the input YAML file, for example, as `analyze.yaml`. Then, we run `gorder` as follows:

```bash
$ gorder analyze.yaml
```

During the analysis, we will see something like this (except colored):

```text
>>> GORDER v0.2.0 <<<

[*] Read config file 'analyze.yaml'.
[*] Will calculate all-atom order parameters.
[*] Membrane normal expected to be oriented along the z axis.
[*] Read molecular topology from 'system.tpr'.
[*] Detected 8768 heavy atoms using a query '@membrane and name r'C3([2-9]|1[0-6])|C2([2-9]|1[0-8])''.
[*] Detected 21592 hydrogen atoms using a query '@membrane and element name hydrogen'.
[*] Detecting molecule types...
[*] Detected 3 relevant molecule type(s).
[*] Molecule type POPE: 64 order bonds, 131 molecules.
[*] Molecule type POPC: 64 order bonds, 128 molecules.
[*] Molecule type POPG: 64 order bonds, 15 molecules.
[*] Will read trajectory file 'md.xtc' (start: 0 ps, end: inf ps, step: 1).
[*] Performing the analysis using 1 thread(s)...
[COMPLETED]   Step    225000000 | Time       450000 ps
[*] Writing the order parameters into a yaml file 'order.yaml'...
[✔] ANALYSIS COMPLETED
```

> Note that the structure from the TPR file is not analyzed. The TPR file is only used to construct the system and obtain its topology.

The results of the analysis are saved in the `order.yaml` file. Here is an excerpt from the file:

```yaml
# Order parameters calculated with 'gorder v0.2.0' using structure file 'system.tpr' and trajectory file 'md.xtc'.
- molecule: POPE
  order:
    POPE C22 (23):
      total: 0.1098
      bonds:
        POPE H2R (24):
          total: 0.0946
        POPE H2S (25):
          total: 0.125
    POPE C32 (32):
      total: 0.2341
      bonds:
        POPE H2X (33):
          total: 0.2438
        POPE H2Y (34):
          total: 0.2244
  # (...)
- molecule: POPC
  order:
    POPC C22 (32):
      total: 0.1094
      bonds:
        POPC H2R (33):
          total: 0.0953
        POPC H2S (34):
          total: 0.1235
    POPC C32 (41):
      total: 0.2325
      bonds:
        POPC H2X (42):
          total: 0.2405
        POPC H2Y (43):
          total: 0.2245
  # (...)
- molecule: POPG
  order:
    POPG C22 (25):
      total: 0.1081
      bonds:
        POPG H2R (26):
          total: 0.1024
        POPG H2S (27):
          total: 0.1138
    POPG C32 (34):
      total: 0.2028
      bonds:
        POPG H2X (35):
          total: 0.2131
        POPG H2Y (36):
          total: 0.1926
```

`gorder` automatically identified three molecule types and all relevant bonds. Order parameters are reported separately for each molecule type: for each bond type of each molecule type and for each heavy atom type of each molecule type. Order parameters for heavy atom types are obtained by averaging the order parameters of their bonds with hydrogens.

Let's take a closer look at a part of the YAML file:

```yaml
- molecule: POPE         # name of the molecule
  order:                 # order parameters
    POPC C22 (23):       # heavy atom type: residue atom_name (relative_index)
      total: 0.1098      # order parameter of the heavy atom
      bonds:             # bonds of this heavy atom with hydrogens
        POPC H2R (24):   # hydrogen type: residue atom_name (relative_index)
          total: 0.0946  # order parameter of bond with this hydrogen
        POPC H2S (25):   # hydrogen type: residue atom_name (relative_index)
          total: 0.125   # order parameter of bond with this hydrogen
```

YAML files are easy to read programmatically and not completely human-unreadable. However, `gorder` also provides other output formats (XVG, CSV, human-readable table). See [Output Formats](output.md) for more information.

## Using groups from an NDX file

`gorder` also supports using groups from NDX files. Let's suppose our NDX file already contains a group called `TailCarbons`, which specifies all the carbons to use. We no longer need to specify them using the complex regular expression from the previous example and can simply select the group. Our input YAML file will look like this:

```yaml
structure: system.tpr
trajectory: md.xtc
index: index.ndx     # name of the NDX file
analysis_type: !AAOrder
  heavy_atoms: "TailCarbons"   # name of a group from NDX file
  hydrogens: "@membrane and element name hydrogen"
output: order.yaml
```

Then, we run the analysis in the same way as before.