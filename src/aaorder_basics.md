# Atomistic order parameters

## Preparing the input

Suppose we have a [CHARMM36](https://academiccharmm.org/) membrane composed of POPC, POPE, and POPG lipids.

To calculate atomistic order parameters, we need two Gromacs files:
- A TPR file containing the system structure and topology (`system.tpr`).
- An XTC trajectory file (`md.xtc`) whose frames will be analyzed.

> It is recommended to use TPR and XTC files, but `gorder` also [supports some other file formats](other_input.md).

Next, we create a configuration YAML file that specifies the options for the analysis:

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !AAOrder
  heavy_atoms: "@membrane and name r'C3.+|C2.+'"
  hydrogens: "@membrane and element name hydrogen"
output: order.yaml
```

In the configuration YAML file, the analysis type `AAOrder` requires you to specify both `heavy_atoms` and `hydrogens`. `gorder` will then identify all bonds connecting the selected heavy atoms with the selected hydrogen atoms. The order parameters are calculated for all these identified bonds.
  
The atoms are selected using a query language called [GSL](gsl.md). If you are familiar with the query language used in VMD, you'll find the basic syntax of GSL intuitive.

Here:
- `heavy_atoms` are selected using the query `@membrane and name r'C3.+|C2.+'`, which selects all palmitoyl and oleoyl carbons of the membrane lipids.
- `hydrogens` are selected using the query `@membrane and element name hydrogen`.

> `@membrane` is a GSL autodetection macro that selects all atoms of common membrane lipids. `r'C3.+|C2.+'` is a regular expression block, natively supported by GSL.

The results of the analysis will be saved in the `order.yaml` file as $-S_{CH}$ (see [Theory](theory.md)).

## Running the analysis

We save the configuration YAML file, for example, as `analyze.yaml`. Then, we run `gorder` as follows:

```bash
$ gorder analyze.yaml
```

During the analysis, we will see something like this:

<img src="charmm.gif" width="620" height="360">

> Note that the structure from the TPR file is not analyzed. The TPR file is only used to construct the system and obtain its topology.

The results of the analysis are saved in the `order.yaml` file. Here is an excerpt from the file:

```yaml
# Order parameters calculated with 'gorder v0.7.1' using a structure file 'system.tpr' and a trajectory file 'md.xtc'.
average order:
  total: 0.1631
POPE:
  average order:
    total: 0.1601
  order parameters:
    POPE C22 (23):
      total: 0.1036
      bonds:
        POPE H2R (24):
          total: 0.0876
        POPE H2S (25):
          total: 0.1196
    POPE C32 (32):
      total: 0.2297
      bonds:
        POPE H2X (33):
          total: 0.2423
        POPE H2Y (34):
          total: 0.2171
  # (...)
POPC:
  average order:
    total: 0.166
  order parameters:
    POPC C22 (32):
      total: 0.1109
      bonds:
        POPC H2R (33):
          total: 0.0935
        POPC H2S (34):
          total: 0.1283
    POPC C32 (41):
      total: 0.2373
      bonds:
        POPC H2X (42):
          total: 0.2483
        POPC H2Y (43):
          total: 0.2264
  # (...)
POPG:
  average order:
    total: 0.1608
  order parameters:
    POPG C22 (25):
      total: 0.0987
      bonds:
        POPG H2R (26):
          total: 0.08
        POPG H2S (27):
          total: 0.1174
    POPG C32 (34):
      total: 0.2272
      bonds:
        POPG H2X (35):
          total: 0.2367
        POPG H2Y (36):
          total: 0.2177
```

`gorder` automatically identified three molecule types and all relevant bonds. Order parameters are reported separately for each molecule type: for each bond type of each molecule type and for each heavy atom type of each molecule type. Order parameters for heavy atom types are obtained by averaging the order parameters of their bonds with hydrogens. `average order` corresponds to the average order of all the relevant bonds of the entire system or a single molecule type, respectively.

> The atom types (and molecule types) are listed in the same order as they appear in the input TPR structure. Note that parameters for C21 and C31 are absent, even though these atoms should qualify as `heavy_atoms` based on the regular expression `C3.+|C2.+`. However, these atoms lack bonded hydrogens and are therefore automatically excluded from the output.

Let's take a closer look at a part of the output YAML file:

```yaml
average order:
  total: 0.1631          # average order calculated for all molecules in the entire membrane
POPE:                    # name of the molecule type
  average_order:
    total: 0.1601        # average order calculated for POPE molecules in the entire membrane
  order parameters:
    POPC C22 (23):       # heavy atom type: residue atom_name (relative_index)
      total: 0.1036      # order parameter of the heavy atom
      bonds:             # bonds of this heavy atom with hydrogens
        POPC H2R (24):   # hydrogen type: residue atom_name (relative_index)
          total: 0.0876  # order parameter of bond with this hydrogen
        POPC H2S (25):   # hydrogen type: residue atom_name (relative_index)
          total: 0.1196  # order parameter of bond with this hydrogen
```

YAML files are easy to read programmatically and not completely human-unreadable. However, `gorder` also provides other output formats (XVG, CSV, human-readable table). See [Output formats](output.md) for more information.

## Using groups from an NDX file

`gorder` also supports using groups from NDX files. Let's suppose our NDX file already contains a group called `TailCarbons`, which specifies all the carbons to use. We no longer need to specify them using the complex regular expression from the previous example and can simply select the group. Our configuration YAML file will look like this:

```yaml
structure: system.tpr
trajectory: md.xtc
index: index.ndx               # name of the NDX file
analysis_type: !AAOrder
  heavy_atoms: "TailCarbons"   # name of a group from NDX file
  hydrogens: "@membrane and element name hydrogen"
output: order.yaml
```

Then, we run the analysis in the same way as before.