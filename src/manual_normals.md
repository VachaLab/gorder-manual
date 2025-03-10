# Manual membrane normals

For absolute control over membrane normals, `gorder` allows manual specification of membrane normals for individual molecules in individual trajectory frames.

To assign membrane normals, you must first prepare a YAML-formatted membrane normals file. The structure of this file is similar to the ["leaflet assignment file"](manual_leaflets.md#leaflet-assignment-file), which can be used to manually assign lipids to leaflets.

## Membrane normals file

Consider a small system consisting of 12 POPE molecules, 10 POPC molecules, and 8 POPG molecules. We are analyzing a very short trajectory of just three frames.

The membrane normals file for this system might look like the following:

```yaml
POPE:
  # First frame
  - - [0.45298508, -0.46554238, 0.7603123]  # Membrane normal for the first POPE molecule
    - [0.2806614, -0.15393148, 0.9473829]   # Membrane normal for the second POPE molecule   
    - [0.43698093, 0.8893582, 0.13449812]   # Membrane normal for the third POPE molecule
    - [0.9750763, 0.21927911, -0.03380459]
    - [0.92026454, -0.104457006, 0.37709686]
    - [0.02029758, -0.70020616, -0.71365213]
    - [0.40990028, -0.20503402, -0.88878727]
    - [0.0019937176, -0.2997502, 0.9540157]
    - [0.8564996, 0.47032556, 0.21260813]
    - [0.049487855, 0.96054417, 0.27368945]
    - [0.94329506, -0.10884861, 0.3136024]
    - [0.088020176, 0.5334338, -0.8412495]
  # Second frame
  - - [0.79301494, -0.24298465, 0.5586464]
    - [0.40446714, -0.19909416, 0.8926186]
    - [0.52881354, 0.8245486, 0.20118622]
    - [-0.87407386, -0.47928867, -0.07922962]
    - [0.9209915, 0.08520295, 0.38015157]
    - [0.3882479, -0.5707272, -0.7235565]
    - [0.7917376, -0.2956609, -0.534543]
    - [0.1430331, -0.5194693, 0.842433]
    - [0.94468683, 0.19440018, 0.26415047]
    - [0.02312048, 0.87527585, 0.4830711]
    - [0.9681385, -0.2009886, 0.14937]
    - [0.19331127, 0.4733897, -0.8593794]
  # Third frame
  - - [-0.5097876, 0.10576716, -0.8537739]
    - [0.5334542, -0.14081219, 0.8340255]
    - [0.4445155, 0.89264977, 0.07471584]
    - [0.8339677, 0.37858394, -0.4014625]
    - [0.8302863, -0.022482118, 0.5568835]
    - [-0.20055892, 0.6154054, 0.76226795]
    - [0.75300694, 0.113635726, -0.6481262]
    - [0.38372648, -0.041432776, 0.9225169]
    - [0.9821218, 0.18594055, -0.02937515]
    - [-0.33398506, -0.92359644, -0.18821158]
    - [0.9435965, 0.33108303, -0.0031385422]
    - [0.18465553, 0.64861244, -0.73837954]
POPC:
  # First frame
  - - [0.83283865, 0.16904251, -0.5270716]  # Membrane normal for the first POPC molecule
    - [0.05139073, -0.5703374, -0.8198014]  # Membrane normal for the second POPC molecule
    - [0.9312034, -0.23388125, -0.2795709]
    - [0.7521123, -0.58389324, 0.3056073]
    - [0.112122506, 0.26633972, 0.9573357]
    - [0.4494933, -0.8491345, 0.2773562]
    - [0.27921712, -0.5408516, -0.7934214]
    - [0.024991032, 0.5270191, 0.84948593]
    - [0.290388, 0.68315774, 0.6700525]
    - [0.3266205, 0.56352586, 0.75878704]
  # Second frame
  - - [0.831447, 0.03588961, -0.55444366]
    # ...and 9 more membrane normals for the remaining POPC molecules
  # Third frame
  - - [0.9958207, -0.07853093, -0.046626113]
    # ...and 9 more membrane normals for the remaining POPC molecules
POPG:
  # Similar structure to POPE and POPC, but only 8 membrane normals per frame
```

Each innermost vector in the file represents the membrane normal assigned to a lipid molecule of the specified type. The molecules are listed in the order in which their **first atoms** appear in the input structure file. Each list of membrane normal vectors corresponds to one trajectory frame.

> Unlike manual lipid assignment, membrane normals must be provided for every individual trajectory frame. `gorder` strictly validates the input membrane normals file. The file must contain **exactly** the same number of frames as the number of analyzed frames in the trajectory. Additionally, it must include the correct number of lipid molecules for each lipid type, and all lipid types being analyzed (and no additional ones) must be specified. Failure to meet these requirements will result in an error.

## Using the membrane normals file

Save the membrane normals file, for example, as `normals.yaml`. To use this file with `gorder`, reference it in the YAML configuration file as follows:

```yaml
membrane_normal: !FromFile normals.yaml
```

## Specifying membrane normals inline

You can also define membrane normals directly within the YAML configuration file:

```yaml
membrane_normal: !Inline
  POPE:
    - - [0.45298508, -0.46554238, 0.7603123] 
      - [0.2806614, -0.15393148, 0.9473829] 
      - [0.43698093, 0.8893582, 0.13449812]
      - [0.9750763, 0.21927911, -0.03380459]
      - [0.92026454, -0.104457006, 0.37709686]
      - [0.02029758, -0.70020616, -0.71365213]
      - [0.40990028, -0.20503402, -0.88878727]
      - [0.0019937176, -0.2997502, 0.9540157]
      - [0.8564996, 0.47032556, 0.21260813]
      - [0.049487855, 0.96054417, 0.27368945]
      - [0.94329506, -0.10884861, 0.3136024]
      - [0.088020176, 0.5334338, -0.8412495]
# (...)
  POPC:
# (...)
  POPG:
# (...)
```