# Manual lipid assignment to leaflets

If you do not want to use the automatic leaflet classification (described in [Order parameters for individual leaflets](leaflets.md)), whether due to your system's complicated geometry or a lack of trust in the `gorder` assignment, you can manually specify the leaflet occupied by each lipid molecule (for each analyzed frame).

There are two ways to manually assign lipids to membrane leaflets: using [NDX files](#assigning-lipids-using-ndx-files) or a ["leaflet assignment file"](#assigning-lipids-using-leaflet-assignment-file).

## Assigning lipids using NDX file(s)

Using NDX files for lipid assignment is arguably a simpler method that enables seamless integration of `gorder` with [`FATSLiM`](https://fatslim.github.io/), which implements possibly the most reliable lipid classification method available.

### NDX file

To begin, create an NDX file containing at least two groups. One group should contain lipid atoms (preferably just their 'head identifiers') for the upper leaflet, while the other group should contain lipid atoms for the lower leaflet. 

```
[ UpperLeaflet ]
1140 1274 1810 2078 2212 2480 2614 2748 2882 3150 3284 3418 3686 3954 4088
4356 4490 4758 5026 5294 5428 5964 6098 6232 6366 6634 6902 7438 7572 7706
7974 8376 8778 8912 9046 9180 9582 9716 9850 10118 10386 10520 10654 10788 11056
11458 11592 11726 11860 12128 12262 12530 12664 13066 13200 13334 13602 14004 14272 14540
14808 14942 15076 15344 15478 15612 16014 16416 16818 17086 17354 17488 17622 17756 17890
18024 18158 18426 18694 18828 18962 19498 20034 20436 21106 21240 21508 21642 21776 21910
[ LowerLeaflet ]
1006 1408 1542 1676 1944 2346 3016 3552 3820 4222 4624 4892 5160 5562 5696
5830 6500 6768 7036 7170 7304 7840 8108 8242 8510 8644 9314 9448 9984 10252
10922 11190 11324 11994 12396 12798 12932 13468 13736 13870 14138 14406 14674 15210 15746
15880 16148 16282 16550 16684 16952 17220 18292 18560 19096 19230 19364 19632 19766 19900
20168 20302 20570 20704 20838 20972 21374 22044 22178 22312 22580 23116 23384 23518 23652
```

### Using the NDX file

Then, specify the classification method in your configuration file:

```yaml
leaflets: !FromNdx
  ndx: "leaflets.ndx"
  heads: "name P"
  upper_leaflet: "UpperLeaflet"
  lower_leaflet: "LowerLeaflet"
  frequency: !Once
```

The leaflet assignment will be read from the NDX file `leaflets.ndx`, which should contain two groups: `UpperLeaflet` and `LowerLeaflet`. These groups should include the atoms of the upper and lower leaflets, respectively. The `heads` field specifies the head identifiers for all analyzed lipid molecules. Note that each lipid molecule must have only one head identifier; otherwise, an error will be returned. Since we are using a single NDX file, we also need to specify `frequency: !Once`, ensuring that the leaflet assignment from the NDX file is applied to all analyzed trajectory frames.

The `UpperLeaflet` and `LowerLeaflet` groups may contain various atoms, but they **must** include all `heads` atoms for the lipids being analyzed. In other words, every analyzed lipid must be assigned to a leaflet. The NDX file itself can contain any number of additional groups, but for performance reasons, it is recommended to keep both the file and the specified groups as lightweight as possible.

### Scrambling-safe leaflet assignment

Instead of using a single NDX file, you can provide multiple NDX files, such as one file per trajectory frame. Your configuration file may then look like this:

```yaml
leaflets: !FromNdx
#          first frame         second frame        third frame         fourth frame
  ndx: ["leaflets0001.ndx", "leaflets0002.ndx", "leaflets0003.ndx", "leaflets0004.ndx", (...)]
  heads: "name P"
  upper_leaflet: "UpperLeaflet"
  lower_leaflet: "LowerLeaflet"
```

With this setup, leaflet assignment will be performed for each frame using a different NDX file.

### Assignments for every Nth frame

If you want to specify leaflet assignments for every Nth analyzed frame (e.g., every 10th frame), follow these steps:

1. Set the appropriate frequency in the configuration file. For example:
  ```yaml
  leaflets: !FromNdx
    ndx: ["leaflets0001.ndx", "leaflets0002.ndx", "leaflets0003.ndx", "leaflets0004.ndx", (...)]
    heads: "name P"
    upper_leaflet: "UpperLeaflet"
    lower_leaflet: "LowerLeaflet"
    frequency: !Every 10
  ```

2. **Ensure that the number of provided NDX files exactly matches the number of analyzed frames divided by N.** For example, if the trajectory is 10,000 frames long, [every 5th frame is analyzed](timerange.md), and leaflet assignment is performed for every 10th analyzed frame, you must provide 200 NDX files. If you provide less **or more** NDX files, you will get an error during the analysis.

***

## Assigning lipids using a leaflet assignment file

Another method for manually assigning lipids is using a YAML-formatted "leaflet assignment file". Unlike the NDX file method, where groups of atoms are used for assignment, the leaflet classification in this method is provided at the molecular level, with each molecule type having its own section in the file.

### Leaflet assignment file

Consider a very small system consisting of 12 POPE molecules, 10 POPC molecules, and 8 POPG molecules. Let's say that this membrane is asymmetric:

- The upper leaflet contains 12 POPE molecules and 4 POPG molecules.
- The lower leaflet contains 10 POPC molecules and 4 POPG molecules.

The leaflet assignment file for this system should look as follows:

```yaml
POPE:
  - [Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper]
POPC:
  - [Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower]
POPG:
  - [Upper, Upper, Upper, Upper, Lower, Lower, Lower, Lower]
```

Each position in each list corresponds to the leaflet occupied by one lipid molecule of the specified type. The molecules are listed in the order in which their **first atoms** appear in the input structure file.

> **Note:** You can substitute `Upper` and `Lower` with `1` and `0`, respectively, for specifying the 'upper' and 'lower' leaflets.

### Using the leaflet assignment file

Save the leaflet assignment file, for example, as `assignment.yaml`. To use this file with `gorder`, include it in the YAML configuration file as follows:

```yaml
leaflets: !FromFile
  file: assignment.yaml
  frequency: !Once
```

> Ensure the frequency is set to `!Once`, as this leaflet assignment file provides information for only a single frame.

### Inline specification of leaflet assignment

You can also embed the leaflet assignment directly in the configuration file:

```yaml
leaflets: !Inline
  assignment:
    POPE:
      - [Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper]
    POPC:
      - [Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower]
    POPG:
      - [Upper, Upper, Upper, Upper, Lower, Lower, Lower, Lower]
  frequency: !Once
```

### Scrambling-safe leaflet assignment

If lipid flip-flop occurs in your system, you may want to specify the leaflet assignment for each analyzed frame. For example:

```yaml
POPE:
  # first analyzed frame
  - [Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper]
  # second analyzed frame
  - [Upper, Upper, Lower, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper]
  # third analyzed frame
  - [Upper, Upper, Lower, Upper, Upper, Upper, Upper, Lower, Upper, Upper, Upper, Upper]
  # (...)
POPC:
  - [Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower]
  - [Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Upper]
  - [Lower, Lower, Lower, Upper, Lower, Lower, Lower, Lower, Lower, Upper]
  # (...)
POPG:
  - [Upper, Upper, Upper, Upper, Lower, Lower, Lower, Lower]
  - [Upper, Upper, Upper, Upper, Lower, Lower, Lower, Lower]
  - [Upper, Upper, Lower, Upper, Upper, Lower, Lower, Lower]
  # (...)
```

To use this expanded file, specify it in the YAML configuration file:

```yaml
leaflets: !FromFile
  file: assignment.yaml
  frequency: !Every 1
```

Alternatively, since `frequency: !Every 1` is the default setting, you can simplify the configuration:

```yaml
leaflets: !FromFile assignment.yaml
```

### Assignments for every Nth frame

If you want to specify leaflet assignments for every Nth analyzed frame (e.g., every 10th frame), you must:

1. Set the appropriate frequency in the configuration file. For example:
    ```yaml
    leaflets: !FromFile
      file: assignment.yaml
      frequency: !Every 10
    ```
2. **Ensure that the number of frames in the leaflet assignment file exactly matches the number of analyzed frames divided by N.** For example, if the trajectory is 10,000 frames long, [every 5th frame is analyzed](timerange.md), and leaflet assignment is performed for every 10th analyzed frame, the leaflet assignment file must contain information for 200 frames.

> **Note:** `gorder` is **very opinionated** when validating the leaflet assignment file. The file must specify the exact same number of lipid molecules for each molecule type as are present in the analyzed molecular system. Additionally, it must contain **exactly** the number of frames required for the leaflet assignment for the specified trajectory at the specified classification frequency (i.e., not a single unused frame can be present in the leaflet assignment file). The leaflet assignment file also cannot include information about molecule types that do not exist in the system. If any of these criteria are not met, `gorder` will reject the leaflet assignment file. This strict validation reduces the chance of errors, as mistakes in manual assignments are easy to make but can be very difficult to detect otherwise.