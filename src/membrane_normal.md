# Specifying membrane normal

`gorder` supports both `static` (provided by the user and applied to all lipid molecules throughout the entire analysis) and `dynamic` (calculated for each lipid molecule in each trajectory frame based on the actual membrane shape) membrane normal specification.

For planar membranes, the default static membrane normal is usually sufficient. However, for vesicles, you should always use dynamic membrane normal calculation. Keep in mind that computing the membrane normal dynamically is computationally expensive.

For complete control over membrane normals, you can also manually assign them for each molecule in every trajectory frame. For more details, refer to [Manual membrane normals](manual_normals.md).

## Static membrane normal

By default, `gorder` assumes the membrane normal is oriented along the z-axis, meaning the membrane is built in the xy-plane. If your membrane is oriented differently, you can manually specify the membrane normal in the configuration YAML file:

```yaml
membrane_normal: x
```

In this example, the membrane normal is oriented along the x-axis, which means that the membrane is built in the yz-plane.

> Only the primary axes of an orthogonal simulation box (`x`, `y`, and `z`) are supported as static membrane normals.

## Dynamic membrane normal

If your membrane is non-planar, or if you want to use the actual membrane normal instead of an idealized direction, you can enable dynamic membrane normal calculation. In this case, you must specify the selection of 'head identifier' atoms (similar to when [assigning lipids to leaflets](leaflets.md)):

```yaml
membrane_normal: !Dynamic
  heads: "name P"
```

These 'head identifiers' are used to estimate the membrane surface and calculate the membrane normal for each lipid molecule. For consistency with leaflet classification, **each analyzed lipid molecule must have exactly one head identifier**; otherwise, an error will be raised.

The membrane normal is estimated for each lipid molecule in every frame. This is done by collecting the positions of 'head identifiers' located within a sphere around the molecule’s own head identifier and performing principal component analysis (PCA) on these positions. The last principal component (the direction with the least variation in atom positions) is then used as the membrane normal.

By default, the radius of the 'scanning sphere' is **2 nm**, but you can adjust it in the input file:

```yaml
membrane_normal: !Dynamic
  heads: "name P"
  radius: 1.5
```

The 'scanning sphere' must meet several requirements:  
- It must be large enough to include a sufficient number of lipid heads for reasonable principal component analysis.
- It must be small enough to capture local membrane curvature correctly.
- Most importantly, it must be **small enough to avoid including head identifiers from the opposite membrane leaflet**, as this would distort the calculated membrane normal.

As a general rule of thumb, set the radius to approximately **half of the membrane thickness**.

### Limitations

When using dynamic membrane normal calculation, you should be aware of some limitations:

1. **Ignoring periodic boundary conditions**  
When [ignoring periodic boundary conditions](no_pbc.md), membrane normals for lipids located near the edges (and especially at the corners) of the simulation box might not be calculated accurately. This occurs because, without PBC, not all nearby lipid heads are included in the surface estimation. If periodic boundary conditions are considered (which is the default), this issue does not occur.

2. **Assigning lipids to leaflets**  
All methods for classifying lipids into leaflets use the membrane normal to determine what is 'up' and what is 'down' in the membrane. However, due to technical limitations, these methods cannot use dynamically calculated membrane normals and always require a static membrane normal. You can specify this manually, for example:

    ```yaml
    leaflets: !Global
      membrane: "@membrane" 
      heads: "name P"
      membrane_normal: z
    ```

    This membrane normal definition is used only when assigning lipids into leaflets. If you are working with a (reasonably) planar membrane, the membrane normal for leaflet classification does not need to be precise, so this approach is perfectly fine, even if you are otherwise working with dynamically calculated membrane normals. However, for a curved membrane, such as a vesicle, you should [assign lipids into leaflets manually](manual_leaflets.md) instead.

3. **Constructing ordermaps**  
When constructing ordermaps, the plane in which an ordermap is generated is determined by the provided membrane normal. Since ordermaps can only be constructed in the `xy`, `xz`, or `yz` plane, they also require a static membrane normal (`z`, `y`, or `x`). If you are calculating the membrane normal dynamically and also want to construct ordermaps, you must specify the ordermap plane manually:

    ```yaml
    ordermaps:
      output_directory: "ordermaps"
      plane: xy
    ```