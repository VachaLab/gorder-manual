# Structure and topology file formats

In rare cases where `gorder` cannot read your input TPR file, or if no TPR file is available, you can provide the system structure and topology using alternative file formats. `gorder` supports GRO, PDB, and PQR files as structure files.

For example, the following YAML input file is valid if the provided PDB file includes a [connectivity section](https://www.wwpdb.org/documentation/file-format-content/format33/sect10.html) *(and has fewer than 100,000 atoms due to PDB format limitations)*:

```yaml
structure: system.pdb
trajectory: md.xtc
analysis_type: !CGOrder
  beads: "@membrane"
output: order.yaml
```

If the PDB file lacks a connectivity section or if you are using a GRO or PQR file as the input structure, you must also supply a "bonds" file:

```yaml
structure: system.gro
bonds: system.bnd   # the file extension does not matter here
trajectory: md.xtc
analysis_type: !CGOrder
  beads: "@membrane"
output: order.yaml
```

Connectivity information will be read from `system.bnd`. *Note that the connectivity data in the bonds file **overrides** even information in the provided PDB or TPR file.*

> `gorder` identifies the file format based on the file extension: `.tpr` for TPR files, `.gro` for GRO files, `.pdb` for PDB files, and `.pqr` for PQR files. Ensure that the file is named correspondingly.

## Specification of the bonds file

The bonds file format is similar to the PDB connectivity block but simpler and much more flexible. It also supports systems of any (reasonable) size.

Each line in the file contains numbers separated by whitespace. The first number specifies the "target atom" (serial number), and the following numbers specify the atoms bonded to it. Serial numbers correspond to Gromacs numbering, where the first atom is `1`, the second is `2`, and so on (no matter the "atom number" in the input GRO or PDB file).

```bnd
1 2 4
```

In this example, atom `1` is bonded to atoms `2` and `4`. Atoms `2` and `4` are **not** bonded to each other.

Each bond needs to be specified only once (with any order of atoms), but duplicating entries is allowed. For instance, the following bonds files are equivalent:

```bnd
1 2 4
2 3
```

```bnd
1 2 4
2 1 3
3 2
4 1
```

*(The system contains three bonds: atoms `1` and `2`, atoms `1` and `4`, and atoms `2` and `3`.)*

Bonds can be listed in any order, and multiple lines can start with the same target atom. Any number of bonds can be specified on one line. Empty lines are ignored, and any amount of whitespace is allowed between the numbers. Comments can be added using `#`:

```bnd
1 2 4   # atom 1 is bonded to atoms 2 and 4
2 3
```

Here’s an excerpt from an example bonds file:

```bnd
# Example bonds file for an atomistic system
1 2 3 4 5
2 1
3 1
4 1
5 1 6 7 8
6 5
7 5
8 5 9 10 15
9 8
10 8
11 12 13 14 15
12 11
13 11
14 11 16
15 8 11
16 14 17 18 19
# (...)
```

## Note about selecting elements

When using the `element` keyword in [GSL](https://ladme.github.io/gsl-guide/) atom selection queries, atoms are selected based on their associated element. Element information is natively available in TPR files but is missing in other supported formats. If a non-TPR file is used, `gorder` will attempt to guess the elements of atoms based on the atom and residue names.

If `gorder` detects potential issues with the guess, it will display a warning in the terminal. You can then evaluate whether the concerns are harmless, avoid using the `element` keyword, or provide a TPR file instead.
