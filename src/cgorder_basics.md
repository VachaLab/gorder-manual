# Coarse-grained order parameters

## Preparing the input

Suppose we have a [Martini 3](https://cgmartini.nl/) membrane composed of POPC, POPE, and POPG lipids.

To calculate coarse-grained order parameters, we need two Gromacs files:
- A TPR file containing the system structure and topology (`system.tpr`).
- An XTC trajectory file (`md.xtc`) whose frames will be analyzed.

Next, we create an input YAML file that specifies the options for the analysis:

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !CGOrder
  beads: "@membrane"
output: order.yaml
```

In the input YAML file, the analysis type `CGOrder` requires you to specify beads that will be considered for the analysis. `gorder` will then identify all bonds connecting the selected beads. The order parameters are calculated for all these identified bonds.

The atoms are selected using a query language called [GSL](https://ladme.github.io/gsl-guide/). If you are familiar with the query language used in VMD, you'll find the basic syntax of GSL intuitive.

Here `beads` are selected using the query `@membrane`. That is a GSL autodetection macro that select all beads or atoms of common membrane lipids.

The results of the analysis will be saved in the `order.yaml` file as $S$ (see [Theory](theory.md)).

## Running the analysis

We save the input YAML file, for example, as `analyze.yaml`. Then, we run `gorder` as follows:

```bash
$ gorder analyze.yaml
```

During the analysis, we will see something like this (except colored):

```text
>>> GORDER v0.3.0 <<<

[*] Read config file 'analyze.yaml'.
[*] Will calculate coarse-grained order parameters.
[*] Membrane normal expected to be oriented along the z axis.
[*] Read molecular topology from 'system.tpr'.
[*] Detected 6096 beads for order calculation using a query '@membrane'.
[*] Detecting molecule types...
[*] Detected 3 relevant molecule type(s).
[*] Molecule type POPC: 11 order bonds, 242 molecules.
[*] Molecule type POPE: 11 order bonds, 242 molecules.
[*] Molecule type POPG: 11 order bonds, 24 molecules.
[*] Will read trajectory file 'md.xtc' (start: 0 ps, end: inf ps, step: 1).
[*] Performing the analysis using 1 thread(s)...
[COMPLETED]   Step     50000000 | Time      1000000 ps
[*] Writing order parameters into a yaml file 'order.yaml'...
[✔] ANALYSIS COMPLETED
```

> Note that the structure from the TPR file is not analyzed. The TPR file is only used to construct the system and obtain its topology.

The results of the analysis are saved in the `order.yaml` file. Here is an excerpt from the file:

```yaml
# Order parameters calculated with 'gorder v0.3.0' using structure file 'system.tpr' and trajectory file 'md.xtc'.
POPC:
  average order:
    total: 0.2943
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
    total: 0.2972
  order parameters:
    POPE NH3 (0) - POPE PO4 (1):
      total: -0.1293
    POPE PO4 (1) - POPE GL1 (2):
      total: 0.6072
    POPE GL1 (2) - POPE GL2 (3):
      total: -0.1833
    POPE GL1 (2) - POPE C1A (4):
  # (...)
POPG:
  average order:
    total: 0.3059
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

`gorder` automatically identified three molecule types and all relevant bonds. Order parameters are reported for each bond type of each molecule type. `average_order` corresponds to the average order of all the relevant bonds of a single molecule type.

> The bond types (and molecule types) are listed in the same order their atoms appear in the input TPR structure.

Let's take a closer look at a part of the YAML file:

```yaml
POPC:                             # name of the molecule
  average order:
    total: 0.2943                 # average order calculated for POPC molecules in the entire membrane
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

YAML files are easy to read programmatically and not completely human-unreadable. However, `gorder` also provides other output formats (XVG, CSV, human-readable table). See [Output Formats](output.md) for more information.

## Using groups from an NDX file

`gorder` also supports using groups from NDX files. Let's suppose our NDX file already contains a group called `LipidBeads`, which specifies all the beads to use. We can then simply select the group. Our input YAML file will look like this:

```yaml
structure: system.tpr
trajectory: md.xtc
index: index.ndx        # name of the NDX file
analysis_type: !CGOrder
  beads: "LipidBeads"   # name of a group from NDX file
output: order.yaml
```

Then, we run the analysis in the same way as before.