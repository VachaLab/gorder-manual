# Manual lipid assignment to leaflets

If you do not want to use the automatic leaflet classification (described in [Order parameters for individual leaflets](leaflets.md)), whether due to your system's complicated geometry or a lack of trust in the `gorder` assignment, you can manually specify the leaflet occupied by each lipid molecule (for each analyzed frame).

To do this, you will need a "leaflet assignment" file and must specify its use in the YAML configuration file.

## Leaflet assignment file

The leaflet assignment file is a YAML-formatted document containing information about the leaflet assignment of each lipid molecule. The assignment is provided separately for each molecule type.

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

## Using the leaflet assignment file

Save the leaflet assignment file, for example, as `assignment.yaml`. To use this file with `gorder`, include it in the YAML configuration file as follows:

```yaml
leaflets: !Manual
  file: assignment.yaml
  frequency: !Once
```

> Ensure the frequency is set to `!Once`, as this leaflet assignment file provides information for only a single frame.

### Inline specification of leaflet assignment

You can also embed the leaflet assignment directly in the YAML configuration file:

```yaml
leaflets: !Manual
  assignment:
    POPE:
      - [Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper, Upper]
    POPC:
      - [Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower, Lower]
    POPG:
      - [Upper, Upper, Upper, Upper, Lower, Lower, Lower, Lower]
  frequency: !Once
```

## Scrambling-safe leaflet assignment

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
leaflets: !Manual
  file: assignment.yaml
  frequency: !Every 1
```

Alternatively, since `frequency: !Every 1` is the default setting, you can simplify the configuration:

```yaml
leaflets: !Manual assignment.yaml
```

### Assignments for every Nth frame

If you want to specify leaflet assignments for every Nth analyzed frame (e.g., every 10th frame), you must:

1. Specify the correct frequency in the YAML configuration file. For example:
    ```yaml
    leaflets: !Manual
      file: assignment.yaml
      frequency: !Every 10
    ```
2. **Ensure that the number of frames in the leaflet assignment file exactly matches the number of analyzed frames divided by N.** For example, if the trajectory is 10,000 frames long, [every 5th frame is analyzed](timerange.md), and leaflet assignment is performed for every 10th analyzed frame, the leaflet assignment file must contain information for 200 frames.

> **Note:** `gorder` is **very opinionated** when validating the leaflet assignment file. The file must specify the exact same number of lipid molecules for each molecule type as are present in the analyzed molecular system. Additionally, it must contain **exactly** the number of frames required for the leaflet assignment for the specified trajectory at the specified classification frequency (i.e., not a single unused frame can be present in the leaflet assignment file). The leaflet assignment file also cannot include information about molecule types that do not exist in the system. If any of these criteria are not met, `gorder` will reject the leaflet assignment file. This strict validation reduces the chance of errors, as mistakes in manual assignments are easy to make but can be very difficult to detect otherwise.