# Assigning lipids using NDX file(s)

Using NDX files for lipid assignment is arguably a simpler method that enables seamless integration of `gorder` with [`FATSLiM`](https://fatslim.github.io/), which implements possibly the best lipid classification method available.

## NDX file

To begin, create an NDX file containing at least two groups. One group should contain lipid atoms (preferably just their 'head identifiers') of the upper leaflet, while the other group should contain lipid atoms of the lower leaflet. 

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

## Using the NDX file

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

The `UpperLeaflet` and `LowerLeaflet` groups may contain various atoms, but they **must** include all `heads` atoms for the lipids being analyzed. In other words, every analyzed lipid must be assigned to a leaflet. The NDX file itself can contain any number of additional groups, but for performance reasons, it is recommended to keep both the file and the specified groups as small as possible.

## Scrambling-safe leaflet assignment

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

## Specifying NDX files using `glob` pattern

Instead of manually listing many NDX file paths in the configuration file, you can use glob pattern matching:

```yaml
leaflets: !FromNdx
  ndx: "leaflets*.ndx"
  heads: "name P"
  upper_leaflet: "UpperLeaflet"
  lower_leaflet: "LowerLeaflet"
```

This pattern matches all NDX files in the current directory whose names start with `"leaflets"`, such as `leaflets0001.ndx`, `leaflets0002.ndx`, as well as `leafletsABCD.ndx`, `leafletsX.ndx`, and similar files, if they exist.

> Glob returns files in lexicographic order based on filenames. As a result, `leaflets10.ndx` may appear before `leaflets2.ndx`. Always verify the order of NDX files and ensure that filenames are structured so that lexicographic and numerical ordering align. For example, when dealing with frames 1–9999, use filenames like `leaflets0001.ndx` to `leaflets9999.ndx` to maintain the correct order.

## Assignments for every Nth frame

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