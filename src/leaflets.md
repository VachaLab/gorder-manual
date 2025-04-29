# Order parameters for individual leaflets

`gorder` can calculate order parameters for the entire membrane as well as for the individual leaflets. 

To do this, you need to specify a method for classifying lipids into membrane leaflets. By default, `gorder` assigns lipids to membrane leaflets independently for each analyzed frame (this can be customized, see [Classification frequency](#classification-frequency)), making it suitable even for the analysis of membranes where lipids flip-flop between leaflets.

There are four leaflet classification methods available in `gorder`: `global`, `local`, `individual`, and `clustering`. In case you are not satisfied with any of them, you can also [assign lipids into leaflets manually](manual_leaflets.md).

> When calculating order parameters for vesicles or similar highly curved membranes, **you should always assign lipids using the [`clustering` method](#clustering-method-for-leaflet-classification) or [manually](manual_leaflets.md)**.

## Global method for leaflet classification

> Reliable for planar membranes and fast. Recommended for most membranes.

In this method, lipid molecules are assigned to membrane leaflets based on the position of their 'head identifier' relative to the **global** membrane center of geometry. The 'head identifier' is a single atom representing the head of the lipid. If the 'head identifier' is located "above" the membrane center, the lipid is assigned to the upper leaflet; if it is located "below", it is assigned to the lower leaflet.

To use this method, you must specify the 'head identifier' atoms and all atoms that form the membrane. [GSL](https://ladme.github.io/gsl-guide/) is used to define these selections.

```yaml
leaflets: !Global
  membrane: "@membrane" 
  heads: "name P"
```

Here, we use autodetected membrane atoms to calculate the membrane center and select atoms named 'P' (phosphorus atoms of lipids) as head identifiers. **Each analyzed lipid must have exactly one head identifier atom**; otherwise, an error will occur.

## Local method for leaflet classification

> Reliable for planar membranes but slow. Recommended for planar membranes if the `global` and `individual` methods do not work for you.

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

> Less reliable but very fast. Recommended for very large, undulating planar membranes.

In this method, lipid molecules are assigned to membrane leaflets based on the position of their 'head identifier' relative to their 'tail ends'. 'Tail ends' refer to the last heavy atoms or beads of the lipid tails. Each lipid molecule may have multiple 'tail ends', but only one 'head identifier'. If the 'head identifier' is located "above" the 'tail ends', the lipid is assigned to the upper leaflet; if it is located "below", it is assigned to the lower leaflet.

To use this method, you must specify selections for the 'head identifiers' and the 'tail ends':

```yaml
leaflets: !Individual
  heads: "name P"
  methyls: "name C218 C316"
```

In this example, atoms named 'P' (phosphorus atoms of lipids) are used as head identifiers, and 'C218' or 'C316' atoms (the last carbons of oleoyl and palmitoyl chains) are used as tail ends.

## Clustering method for leaflet classification

> Reliable but very slow. Recommended for curved membranes.

This method assigns lipid molecules to membrane leaflets by clustering their head groups using [spectral clustering](https://en.wikipedia.org/wiki/Spectral_clustering). This method can handle any membrane geometry that is physically realistic, including curved membranes with pores or lipid flip-flop.

To use the clustering method, specify a selection for 'head identifiers':

```yaml
leaflets: !Clustering
  heads: "name P"
```

In this example, phosphorus atoms ('P') serve as head identifiers. As with other methods, each lipid molecule must have exactly one head identifier, but you can also include head identifiers for lipid molecules for which you are not calculating the order parameters. The method groups the specified atoms into two clusters representing the membrane leaflets.

### Important considerations for the clustering method
- **Upper vs lower leaflet assignment:** Unlike other methods, clustering does not use the membrane normal direction, so the labels `upper` and `lower` leaflets are set somewhat arbitrarily following these rules:
  - First frame: The more populated cluster becomes the `upper` leaflet. If equal, the cluster containing the lowest-index head identifier is `upper`.
  - Subsequent frames: Clusters are matched to previous frame's leaflets based on similarity.
  - The matching is heuristic in membranes with lipid flip-flop and may fail if more than roughly 20% of lipids change leaflets between two consecutive analyzed frames. An error is raised if 20-80% lipids change leaflets. If more than 80% change leaflets, results will be incorrect without warning (though this is extremely unlikely).
  - The matching may also fail if the spectral clustering identifies the leaflets incorrectly. In such case, you should provide the leaflet assignment manually.
- **Head identifier selection:** When using the clustering method, always select head identifiers for **all** lipids in your membrane—even if analyzing only a specific subset of lipids and particularly when this subset resides in just one membrane leaflet.
- **Extremely slow:** Spectral clustering can be extremely slow, especially when your membrane is large. If you know that there is no flip-flop in your system, it is **highly recommended** to set the classification `frequency` to `!Once` when using this method (see [below](#classification-frequency)).

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

> Using `frequency: !Once` is especially useful for the local and clustering classification methods which are computationally expensive.

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

## Membrane normal

All leaflet classification methods (except for the clustering method) use the specified membrane normal to determine what is 'up' and what is 'down'. If your membrane is planar and aligned with the `xy` plane, no further action is needed. Otherwise, refer to [this section of the manual](membrane_normal.md).

Here, we just mention that the membrane normal used for leaflet classification can be decoupled from the 'global' membrane normal used for calculating order parameters:

```yaml
leaflets: !Global
  membrane: "@membrane"
  heads: "name P"
  membrane_normal: x   # used only for leaflet classification
```


## Leaflet-wise output

When a leaflet classification method is defined, `gorder` calculates order parameters for both the entire membrane and individual leaflets. Leaflet-specific order parameters are included in all `gorder` output formats: YAML, CSV, "table", and XVG.

During analysis, `gorder` also prints information about membrane composition in the first trajectory frame, allowing you to quickly check for any obvious errors. Such membrane composition information may look like this:

```text
(...)
[*] Upper leaflet in the first analyzed frame: POPE: 45, POPC: 50, POPG: 5
[*] Lower leaflet in the first analyzed frame: POPE: 45, POPC: 50, POPG: 5
(...)
```

Below is an excerpt from an output YAML file containing results for individual membrane leaflets:

```yaml
# Order parameters calculated with 'gorder v0.7.1' using a structure file 'system.tpr' and a trajectory file 'md.xtc'.
average order:
  total: 0.1631
  upper: 0.1629
  lower: 0.1632
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