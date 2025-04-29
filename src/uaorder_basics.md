# United-atom order parameters

## Preparing the input

Suppose we have a membrane composed of POPC Berger lipids.

To calculate united-atom order parameters, we need two Gromacs files:
- A TPR file containing the system structure and topology (`system.tpr`).
- An XTC trajectory file (`md.xtc`) whose frames will be analyzed.

> It is recommended to use TPR and XTC files, but `gorder` also [supports some other file formats](other_input.md).

Next, we create a configuration YAML file that specifies the options for the analysis:

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !UAOrder
  # all carbons of the lipid except for carboxyl atoms (C15, C34) and unsatured atoms (C24, C25)
  saturated: "@membrane and element name carbon and not name C15 C34 C24 C25"
  unsaturated: "@membrane and name C24 C25"
output: order.yaml
```

In the configuration YAML file, you must specify which carbons should have their order parameters calculated and whether they are `saturated` or `unsaturated`. `gorder` will automatically construct hydrogens for saturated carbons so that the total number of bonds of each carbon is 4. For example:
- Carbons within the acyl chains (bonded to two other carbons) will be assigned two hydrogens, whose positions are predicted based on the lipid's geometry.
- Methyl carbons at the ends of the acyl chains will have three hydrogens constructed.
- The central carbon of the glycerol will have one hydrogen constructed.

Unsaturated carbons are those with a double bond to another carbon in the acyl chain (and one single bond to another carbon). Only one hydrogen is predicted for such atoms.

In configuration YAML file above, we calculate order parameters for all carbons in POPC lipids (except for the carboxyl atoms C15 and C34, which lack hydrogens and should be explicitly excluded, as explained below). We also specify a double bond between atoms C24 and C25.

> Positions of hydrogens are predicted in exactly the same way as in the `buildH` tool. See the [buildH documentation](https://buildh.readthedocs.io/en/latest/algorithms_Hbuilding.html) for more information.

The results of the analysis will be saved in the `order.yaml` file as $-S_{CH}$ (see [Theory](theory.md)).

### Limitations to consider

There are some limitations you should be aware of when calculating united-atom lipid order parameters:
- Order parameters calculated for individual C-H bonds of **methyl** carbons are **not reliable**. Please read the [note on order parameters of methyl groups](#note-on-order-parameters-of-methyl-groups).
- While the calculation of atomistic and coarse-grained order parameters can be performed for all atom types, the calculation of united-atom parameters must be exclusively performed for **carbon atoms**.
- `gorder` does not have access to the multiplicities of covalent bonds (i.e., it cannot automatically distinguish between single and double bonds). This is why you must specify saturated and unsaturated carbons separately, and why **you should exclude carboxyl carbons from the analysis**. A carboxyl carbon has a double bond to oxygen and is *not* bonded to a hydrogen. However, since `gorder` cannot detect the multiplicity of the double bond, it will attempt to add a hydrogen to the carbon if it is included among saturated carbons. Therefore, you should either exclude the carboxyl carbon entirely or include it among unsaturated atoms.
- If you are calculating united-atom order parameters for unnatural or fictional molecules, `gorder` may not work properly. `gorder` only supports standard lipid molecules, where:
  - No carbon atom is bonded to two double bonds simultaneously.
  - No triple bonds are present.
  - Terminal carbons are methyl groups, not methylidenes.
  - Carbon-hydrogen bonds have a length of about 0.109 nm.

## Running the analysis

We save the configuration YAML file, for example, as `analyze.yaml`. Then, we run `gorder` as follows:

```bash
$ gorder analyze.yaml
```

During the analysis, we will see something like this:

<img src="berger.gif" width="620" height="360">

> Note that the structure from the TPR file is not analyzed. The TPR file is only used to construct the system and obtain its topology.

The results of the analysis are saved in the `order.yaml` file. Here is an excerpt from the file:

```yaml
# Order parameters calculated with 'gorder v0.7.1' using a structure file 'system.tpr' and a trajectory file 'md.xtc'.
average order:
  total: 0.098
POPC:
  average order:
    total: 0.098
  order parameters:
    POPC C1 (0):
      total: 0.0001
      bonds:
      - total: 0.0001
      - total: -0.002
      - total: 0.0021
    POPC C2 (1):
      total: 0.0001
      bonds:
      - total: -0.0
      - total: 0.0022
      - total: -0.0019
    POPC C3 (2):
      total: 0.0001
      bonds:
      - total: -0.0001
      - total: -0.0021
      - total: 0.0025
    POPC C5 (4):
      total: -0.0554
      bonds:
      - total: -0.0665
      - total: -0.0443
    # (...)
    POPC C24 (23):
      total: 0.0723
      bonds:
      - total: 0.0723
    POPC C25 (24):
      total: 0.0046
      bonds:
      - total: 0.0046
    # (...)
    POPC CA2 (51):
      total: 0.0254
      bonds:
      - total: -0.0381
      - total: 0.0572
      - total: 0.0572
```

`gorder` automatically identified one molecule type and all relevant bonds. It then predicted the positions of missing hydrogens for the individual carbons. Order parameters are reported for each carbon of each molecule type, as well as for each predicted C-H bond. `average_order` corresponds to the average order of all the relevant atoms of the entire system or a single molecule type, respectively.

> The atom types (and molecule types) are listed in the same order as they appear in the input TPR structure.

Let's take a closer look at a part of the output YAML file:

```yaml
average order:
  total: 0.098         # average order calculated for all molecules in the entire membrane
POPC:                  # name of the molecule type
  average order:
    total: 0.098       # average order calculated for POPC molecules in the entire membrane
  order parameters:
    POPC C1 (0):       # carbon type: residue atom_name (relative_index)
      total: 0.0001    # order parameter of the carbon
      bonds:           # bonds of this heavy atom with predicted hydrogens
      - total: 0.0001  # order parameter of C-H bond number 1 (predicted hydrogens have no names)
      - total: -0.002  # order parameter of C-H bond number 2
      - total: 0.0021  # order parameter of C-H bond number 3
```

YAML files are easy to read programmatically and not completely human-unreadable. However, `gorder` also provides other output formats (XVG, CSV, human-readable table). See [Output formats](output.md) for more information.

### Note on order parameters of methyl groups

> **This is important! Please read at least the information in this box.** You should not trust the order parameters reported for individual C-H bonds in methyl groups. In methyl groups, only the order parameter calculated for the entire carbon is reliable.

While `gorder` reports order parameters for individual predicted C-H bonds, you might notice that the order parameters for bonds of methyl carbons are somewhat suspicious. Typically, the individual C-H bonds of the same atom have very similar values, but this is not the case here, where the order parameters might differ significantly. For example:

```yaml
    POPC CA2 (51):
      total: 0.0254
      bonds:
      - total: -0.0381   # suspicious
      - total: 0.0572
      - total: 0.0572
```

This discrepancy is an artifact of the hydrogen-building process. The results for individual C-H bonds of methyl carbons depend on the order of atoms that are used as "helpers". For `gorder`, this means that changing the order of atoms in your topology may yield **completely** different results for bonds in methyl groups, even when analyzing the same (but reordered) trajectory.

However, only the order parameters for individual bonds are affected by this artifact. When averaging the order parameters of the individual bonds, the result remains consistent regardless of the helper atom definitions. Thus, the overall order parameter for the atom (the `total` field in the output YAML file) is reasonable and correct *(to the degree united-atom force fields are accurate)* and can be safely reported.

## Using groups from an NDX file

`gorder` also supports using groups from NDX files. Let's suppose our NDX file already contains groups called `Satured` and `Unsaturated`, which specify all carbons to analyze. We can then simply select the groups. Our configuration YAML file will look like this:

```yaml
structure: system.tpr
trajectory: md.xtc
index: index.ndx             # name of the NDX file
analysis_type: !UAOrder
  saturated: "Saturated"     # name of the NDX group containing saturated carbons
  unsaturated: "Unsaturated" # name of the NDX group containing unsaturated carbons
output: order.yaml
```

Then, we run the analysis in the same way as before.

## Ignoring some atoms

In some cases, such as when calculating united-atom order parameters from an atomistic system or when working with a hybrid AA-UA system, you may want to ignore certain atoms so that they are not considered when calculating bonds, obtaining the number of hydrogens to construct for each carbon, or determining helper atoms for predicting hydrogen positions. To do this, add an `ignore` keyword to the `analysis_type` section of your configuration YAML file:

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !UAOrder
  saturated: "@membrane and name r'C3.+|C2.+' and not name C29 C210 C21 C31"
  unsaturated: "@membrane and name C29 C210"
  ignore: "element name hydrogen"
output: order.yaml
```

The above YAML configuration can be used to calculate united-atom lipid order parameters for the tails of atomistic (CHARMM36) POPx lipids (lipids with a palmitoyl and an oleoyl tail). The explicit hydrogen atoms will be ignored, and new hydrogens will be predicted instead.