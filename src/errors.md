# Estimating errors

`gorder` is able to provide estimates of error for the individual bonds, atoms, and entire molecules.

The primary source of error when calculating order parameters from molecular dynamics simulations is the lack of convergence in the simulation. It’s also important to note that an order parameter calculated from a single trajectory frame is very unreliable unless the system is gigantic and contains many molecules. Using the standard deviation or standard error calculated from order parameters of individual frames or even samples is therefore not a viable option. Instead, `gorder` uses block averaging to calculate an error that reflects how well the simulation has converged.

To estimate the error, the trajectory is divided into blocks (default: 5). Each block represents a sequential portion of the trajectory: for example, the first fifth of the frames make up block #1, the second fifth forms block #2, and so on. The average order for each bond is calculated within each block, and a **sample standard deviation** is calculated from these block averages. This standard deviation is reported as the error. For simulations that are well-converged and have large enough blocks, the standard deviation between the blocks will be small.


## Requesting error estimation

By default, `gorder` does not calculate error estimates. To enable error estimation, include the following line in your input YAML file:

```yaml
estimate_error: true
```

The output YAML file will then include error estimates and may look similar to this:

```yaml
# Order parameters calculated with 'gorder v0.3.0' using structure file 'system.tpr' and trajectory file 'md.xtc'.
POPE:
  average order:
    total:
      mean: 0.1455
      error: 0.0029
  order parameters:
    POPE C12 (4):
      total:
        mean: -0.0046
        error: 0.0155
      bonds:
        POPE H12A (5):
          total:
            mean: 0.0021
            error: 0.0242
        POPE H12B (6):
          total:
            mean: -0.0114
            error: 0.0289
    POPE C11 (7):
      total:
        mean: -0.0745
        error: 0.0082
      bonds:
        POPE H11A (8):
          total:
            mean: -0.095
            error: 0.0119
        POPE H11B (9):
          total:
            mean: -0.0539
            error: 0.0087
#(...)
```

The errors are also reported in CSV and table files but **not** in XVG files:

```csv
molecule,residue,atom,relative index,total,total error,hydrogen #1,hydrogen #1 error,hydrogen #2,hydrogen #2 error,hydrogen #3,hydrogen #3 error
POPE,POPE,C12,4,-0.0046,0.0155,0.0021,0.0242,-0.0114,0.0289,,
POPE,POPE,C11,7,-0.0745,0.0082,-0.0950,0.0119,-0.0539,0.0087,,
POPE,POPE,C1,15,0.2155,0.0059,0.2079,0.0116,0.2231,0.0124,,
POPE,POPE,C2,18,0.1914,0.0070,0.1914,0.0070,,,,
(...)
```

```text
Molecule type POPE
                TOTAL       |    HYDROGEN #1    |    HYDROGEN #2    |    HYDROGEN #3    |
C12       -0.0046 ± 0.0155  |  0.0021 ± 0.0242  | -0.0114 ± 0.0289  |                   |
C11       -0.0745 ± 0.0082  | -0.0950 ± 0.0119  | -0.0539 ± 0.0087  |                   |
C1         0.2155 ± 0.0059  |  0.2079 ± 0.0116  |  0.2231 ± 0.0124  |                   |
C2         0.1914 ± 0.0070  |  0.1914 ± 0.0070  |                   |                   |
(...)
```

However, error estimates are currently not available for individual [ordermaps](ordermaps.md).

## Changing the number of blocks

If you are unsatisfied with the default number of blocks (5), you can adjust this parameter:

```yaml
estimate_error:
  n_blocks: 10
```

For example, setting `n_blocks: 10` will use 10 blocks for error estimation instead of the default 5. You should avoid tweaking this number to get a lower error estimate.

## Convergence analysis

`gorder` offers an additional method to assess simulation convergence by visualizing how the average order parameters evolve over time for individual molecules.

To request convergence analysis, include the following lines in your input YAML file:

```yaml
estimate_error:
  output_convergence: convergence.xvg
```

This will not only estimate errors as described above but also generate an XVG file (e.g., `convergence.xvg`) which shows the order parameters calculated for each frame as the average of values from that frame and all preceding frames (i.e., prefix averages).

![Example of convergence plots: POPE and POPC converge quickly, POPG much more slowly](convergence.png)

The example plot illustrates a relatively well-converged atomistic simulation where approximately 3,000 trajectory frames were analyzed. The chart shows that results for POPE and POPC lipids stabilize early (within the first third of the trajectory), while POPG converges more slowly (which is due to the smaller number of these lipids in the system).