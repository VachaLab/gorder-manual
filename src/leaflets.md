# Order parameters for individual leaflets

`gorder` can calculate order parameters for the entire membrane as well as for the individual leaflets. To do this, you need to specify a method for classifying lipids into membrane leaflets. `gorder` assigns lipids to membrane leaflets independently for each frame, making it suitable even for the analysis of membranes where lipids flip-flop between leaflets.

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

> Fast but less reliable. Suitable for large membranes.

In this method, lipid molecules are assigned to membrane leaflets based on the position of their 'head identifier' relative to their 'tail ends'. 'Tail ends' refer to the last heavy atoms or beads of the lipid tails. Each lipid molecule may have multiple 'tail ends', but only one 'head identifier'. If the 'head identifier' is located "above" the 'tail ends', the lipid is assigned to the upper leaflet; if it is located "below", it is assigned to the lower leaflet.

To use this method, you must specify selections for the 'head identifiers' and the 'tail ends'.

```yaml
leaflets: !Individual
  heads: "name P"
  methyls: "name C218 C316"
```

In this example, atoms named 'P' (phosphorus atoms of lipids) are used as head identifiers, and 'C218' or 'C316' atoms (the last carbons of oleoyl and palmitoyl chains) are used as tail ends.

## Example output YAML file

When the leaflet classification method is specified, `gorder` will calculate order parameters for both the entire membrane and for the individual leaflets. Here is an excerpt from an output YAML file containing results for the individual membrane leaflets:

```yaml
# Order parameters calculated with 'gorder v0.2.0' using structure file 'system.tpr' and trajectory file 'md.xtc'.
- molecule: POPE
  order:
    POPE C22 (23):
      total: 0.1098
      upper: 0.1101
      lower: 0.1095
      bonds:
        POPE H2R (24):
          total: 0.0946
          upper: 0.0938
          lower: 0.0955
        POPE H2S (25):
          total: 0.125
          upper: 0.1265
          lower: 0.1235
    POPE C32 (32):
      total: 0.2341
      upper: 0.2309
      lower: 0.2372
      bonds:
        POPE H2X (33):
          total: 0.2438
          upper: 0.2409
          lower: 0.2467
        POPE H2Y (34):
          total: 0.2244
          upper: 0.2209
          lower: 0.2278
  #(...)
- molecule: POPC
  order:
    POPC C22 (32):
      total: 0.1094
      upper: 0.1065
      lower: 0.1124
      bonds:
        POPC H2R (33):
          total: 0.0953
          upper: 0.0928
          lower: 0.0978
        POPC H2S (34):
          total: 0.1235
          upper: 0.1201
          lower: 0.1269
    POPC C32 (41):
      total: 0.2325
      upper: 0.2363
      lower: 0.2287
      bonds:
        POPC H2X (42):
          total: 0.2405
          upper: 0.2455
          lower: 0.2354
        POPC H2Y (43):
          total: 0.2245
          upper: 0.2272
          lower: 0.2219
  #(...)
- molecule: POPG
  order:
    POPG C22 (25):
      total: 0.1081
      upper: 0.0975
      lower: 0.1203
      bonds:
        POPG H2R (26):
          total: 0.1024
          upper: 0.0934
          lower: 0.1128
        POPG H2S (27):
          total: 0.1138
          upper: 0.1016
          lower: 0.1278
    POPG C32 (34):
      total: 0.2028
      upper: 0.2174
      lower: 0.1862
      bonds:
        POPG H2X (35):
          total: 0.2131
          upper: 0.2315
          lower: 0.192
        POPG H2Y (36):
          total: 0.1926
          upper: 0.2033
          lower: 0.1803
  #(...)
```