# Coarse-grained order parameters

## Preparing the input

Suppose we have a [Martini 3](https://cgmartini.nl/) membrane composed of POPC, POPE, and POPG lipids.

To calculate coarse-grained order parameters, we need two Gromacs files:
- A TPR file containing the system structure and topology (`system.tpr`).
- An XTC trajectory file (`md.xtc`) whose frames will be analyzed.

> It is recommended to use TPR and XTC files, but `gorder` also [supports some other file formats](other_input.md).

Next, we create a configuration YAML file that specifies the options for the analysis:

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !CGOrder
  beads: "@membrane"
output: order.yaml
```

In the configuration YAML file, the analysis type `CGOrder` requires you to specify beads that will be considered for the analysis. `gorder` will then identify all bonds connecting the selected beads. The order parameters are calculated for all these identified bonds.

The atoms are selected using a query language called [GSL](gsl.md). If you are familiar with the query language used in VMD, you'll find the basic syntax of GSL intuitive.

Here `beads` are selected using the query `@membrane`. That is a GSL autodetection macro that select all beads or atoms of common membrane lipids.

The results of the analysis will be saved in the `order.yaml` file as $S$ (see [Theory](theory.md)).

## Running the analysis

We save the configuration YAML file, for example, as `analyze.yaml`. Then, we run `gorder` as follows:

```bash
$ gorder analyze.yaml
```

During the analysis, we will see something like this:

<img src="martini.gif" width="620" height="360">

> Note that the structure from the TPR file is not analyzed. The TPR file is only used to construct the system and obtain its topology.

The results of the analysis are saved in the `order.yaml` file. Here is an excerpt from the file:

```yaml
# Order parameters calculated with 'gorder v1.2.0' using a structure file 'system.tpr' and a trajectory file 'md.xtc'.
average order:
  total: 0.2937
POPC:
  average order:
    total: 0.2902
  order parameters:
    POPC NC3 (0) - POPC PO4 (1):
      total: -0.1362
    POPC PO4 (1) - POPC GL1 (2):
      total: 0.585
    POPC GL1 (2) - POPC GL2 (3):
      total: -0.1808
    POPC GL1 (2) - POPC C1A (4):
      total: 0.3835
  # (...)
POPE:
  average order:
    total: 0.2959
  order parameters:
    POPE NH3 (0) - POPE PO4 (1):
      total: -0.1293
    POPE PO4 (1) - POPE GL1 (2):
      total: 0.6072
    POPE GL1 (2) - POPE GL2 (3):
      total: -0.1833
    POPE GL1 (2) - POPE C1A (4):
      total: 0.3965
  # (...)
POPG:
  average order:
    total: 0.3063
  order parameters:
    POPG GL0 (0) - POPG PO4 (1):
      total: 0.0004
    POPG PO4 (1) - POPG GL1 (2):
      total: 0.5917
    POPG GL1 (2) - POPG GL2 (3):
      total: -0.1807
    POPG GL1 (2) - POPG C1A (4):
      total: 0.395
  # (...)
```

`gorder` automatically identified three molecule types and all relevant bonds. Order parameters are reported for each bond type of each molecule type. `average order` corresponds to the average order of all the relevant bonds of the entire system or a single molecule type, respectively.

> ⚠️ The bond types are listed in the **same order their atoms appear in the input TPR structure**. Note that in some force fields, the sequence of atoms may be unintuitive and bonds from different tails may not be separated. Always check the output before plotting the results!

Let's take a closer look at a part of the output YAML file:

```yaml
average order:
  total: 0.2937                   # average order calculated for all molecules in the entire membrane
POPC:                             # name of the molecule type
  average order:
    total: 0.2902                 # average order calculated for POPC molecules in the entire membrane
  order parameters:
    POPC NC3 (0) - POPC PO4 (1):  # bond type (atom type 1 - atom type 2)
      total: -0.1362              # order parameter of this bond
    POPC PO4 (1) - POPC GL1 (2):  # atom types are specified as residue atom_name (relative_index)
      total: 0.585
    POPC GL1 (2) - POPC GL2 (3):
      total: -0.1808
    POPC GL1 (2) - POPC C1A (4):
      total: 0.3835
```

YAML files are easy to read programmatically and not completely human-unreadable. However, `gorder` also provides other output formats (XVG, CSV, human-readable table). See [Output formats](output.md) for more information.

## Using groups from an NDX file

`gorder` also supports using groups from NDX files. Let's suppose our NDX file already contains a group called `LipidBeads`, which specifies all the beads to use. We can then simply select the group. Our configuration YAML file will look like this:

```yaml
structure: system.tpr
trajectory: md.xtc
index: index.ndx        # name of the NDX file
analysis_type: !CGOrder
  beads: "LipidBeads"   # name of a group from NDX file
output: order.yaml
```

Then, we run the analysis in the same way as before.