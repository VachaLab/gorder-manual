# Order parameters for individual leaflets

`gorder` can calculate order parameters for the entire membrane as well as for the individual leaflets. 

To do this, you need to specify a method for classifying lipids into membrane leaflets. By default, `gorder` assigns lipids to membrane leaflets independently for each analyzed frame (this can be customized, see [Classification frequency](#classification-frequency)), making it suitable even for the analysis of membranes where lipids flip-flop between leaflets.

There are three leaflet classification methods available in `gorder`: `global`, `local`, and `individual`.

## Global method for leaflet classification

> Fast and reliable. Recommended, especially good for disrupted membranes.

In this method, lipid molecules are assigned to membrane leaflets based on the position of their 'head identifier' relative to the **global** membrane center of geometry. The 'head identifier' is a single atom representing the head of the lipid. If the 'head identifier' is located "above" the membrane center, the lipid is assigned to the upper leaflet; if it is located "below", it is assigned to the lower leaflet.

To use this method, you must specify the 'head identifier' atoms and all atoms that form the membrane. [GSL](https://docs.rs/groan_rs/latest/groan_rs/#groan-selection-language) is used to define these selections.

```yaml
leaflets: !Global
  membrane: "@membrane" 
  heads: "name P"
```

Here, we use autodetected membrane atoms to calculate the membrane center and select atoms named 'P' (phosphorus atoms of lipids) as head identifiers. **Each analyzed lipid must have exactly one head identifier atom**; otherwise, an error will occur.

## Local method for leaflet classification

> Very slow but reliable. Useful for some curved systems.

In this method, lipid molecules are assigned to membrane leaflets based on the position of their 'head identifier' relative to the **local** membrane center of geometry. The local membrane center is calculated using atoms in a cylinder around the 'head identifier'. If the 'head identifier' is located "above" the local center, the lipid is assigned to the upper leaflet; if "below", it is assigned to the lower leaflet.

For this method, you need to specify a selection of head identifiers, all atoms forming the membrane, and the radius of the cylinder used to define the local membrane.

```yaml
leaflets: !Local
  membrane: "@membrane"
  heads: "name P"
  radius: 2.5
```

Autodetected membrane atoms will be used to calculate the membrane center. Only atoms within a cylinder of radius 2.5 nm (with infinite height) centered on the 'head identifier' and oriented along the membrane normal will be used for the local center calculation. The atoms named 'P' (phosphorus atoms of lipids) are used as 'head identifiers'.

## Individual method for leaflet classification

> Very fast but less reliable. Suitable for large membranes.

In this method, lipid molecules are assigned to membrane leaflets based on the position of their 'head identifier' relative to their 'tail ends'. 'Tail ends' refer to the last heavy atoms or beads of the lipid tails. Each lipid molecule may have multiple 'tail ends', but only one 'head identifier'. If the 'head identifier' is located "above" the 'tail ends', the lipid is assigned to the upper leaflet; if it is located "below", it is assigned to the lower leaflet.

To use this method, you must specify selections for the 'head identifiers' and the 'tail ends'.

```yaml
leaflets: !Individual
  heads: "name P"
  methyls: "name C218 C316"
```

In this example, atoms named 'P' (phosphorus atoms of lipids) are used as head identifiers, and 'C218' or 'C316' atoms (the last carbons of oleoyl and palmitoyl chains) are used as tail ends.

## Classification frequency

By default, `gorder` performs leaflet classification independently for each analyzed frame. This ensures accurate analysis in membranes where lipid exchange occurs between leaflets. However, you can modify this behavior using the `frequency` keyword to specify how often leaflet classification should be performed.

### Once
If you know that lipid flip-flop does not occur in your system and want to accelerate the analysis, you can use `frequency: !Once`. This option assigns lipids to individual membrane leaflets based on the **first** trajectory frame (**not** the TPR file structure), and this assignment is then used for all subsequent trajectory frames.

Example usage:
```yaml
leaflets: !Local
  membrane: "@membrane"
  heads: "name P"
  radius: 2.5
  frequency: !Once
```

> Using `frequency: !Once` is especially useful for the Local classification method which is very computationally expensive.

### Every N frames
Alternatively, you can specify that classification should occur every N analyzed trajectory frames using `frequency: !Every N`. For example, `frequency: !Every 10` means that classification will be performed every 10 analyzed trajectory frames, with the closest previous assignment used for intermediate frames.

Example usage:
```yaml
leaflets: !Global
  membrane: "@membrane"
  heads: "name P"
  frequency: !Every 10
```

> **Important note:** The frequency applies to **analyzed** trajectory frames. For instance, if the classification frequency is set to 10 and the analysis step size is 5 (see [Analyzing a part of the trajectory](timerange.md)), leaflet classification will occur every **50th** (10×5) frame in the input trajectory.

## Leaflet-wise output

When a leaflet classification method is defined, `gorder` calculates order parameters for both the entire membrane and individual leaflets. Leaflet-specific order parameters are included in all `gorder` output formats: YAML, CSV, "table", and XVG.

During analysis, `gorder` also prints information about membrane composition in the first trajectory frame, allowing you to quickly check for any obvious errors. Such membrane composition information may look like this:

```text
(...)
[*] Upper leaflet in the first analyzed frame: POPC: 128
[*] Lower leaflet in the first analyzed frame: POPE: 131, POPG: 15
(...)
```

Below is an excerpt from an output YAML file containing results for individual membrane leaflets:

```yaml
# Order parameters calculated with 'gorder v0.3.0' using structure file 'system.tpr' and trajectory file 'md.xtc'.
POPE:
  average order:
    total: 0.1601
    upper: 0.1603
    lower: 0.1598
  order parameters:
    POPE C22 (23):
      total: 0.1036
      upper: 0.1069
      lower: 0.1003
      bonds:
        POPE H2R (24):
          total: 0.0876
          upper: 0.0924
          lower: 0.0828
        POPE H2S (25):
          total: 0.1196
          upper: 0.1214
          lower: 0.1178
    POPE C32 (32):
      total: 0.2297
      upper: 0.2291
      lower: 0.2303
      bonds:
        POPE H2X (33):
          total: 0.2423
          upper: 0.241
          lower: 0.2437
        POPE H2Y (34):
          total: 0.2171
          upper: 0.2173
          lower: 0.2168
#(...)
POPC:
  average order:
    total: 0.166
    upper: 0.1654
    lower: 0.1665
  order parameters:
    POPC C22 (32):
      total: 0.1109
      upper: 0.1117
      lower: 0.1101
      bonds:
        POPC H2R (33):
          total: 0.0935
          upper: 0.0966
          lower: 0.0904
        POPC H2S (34):
          total: 0.1283
          upper: 0.1268
          lower: 0.1297
    POPC C32 (41):
      total: 0.2373
      upper: 0.236
      lower: 0.2387
      bonds:
        POPC H2X (42):
          total: 0.2483
          upper: 0.2446
          lower: 0.2519
        POPC H2Y (43):
          total: 0.2264
          upper: 0.2273
          lower: 0.2255
#(...)
POPG:
  average order:
    total: 0.1608
    upper: 0.1621
    lower: 0.1594
  order parameters:
    POPG C22 (25):
      total: 0.0987
      upper: 0.103
      lower: 0.0944
      bonds:
        POPG H2R (26):
          total: 0.08
          upper: 0.0841
          lower: 0.0759
        POPG H2S (27):
          total: 0.1174
          upper: 0.1219
          lower: 0.1129
    POPG C32 (34):
      total: 0.2272
      upper: 0.2293
      lower: 0.2251
      bonds:
        POPG H2X (35):
          total: 0.2367
          upper: 0.2391
          lower: 0.2342
        POPG H2Y (36):
          total: 0.2177
          upper: 0.2195
          lower: 0.2159
#(...)
```