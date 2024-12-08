# Analyzing a part of the trajectory

By default, `gorder` analyzes the entire provided trajectory, meaning it processes all frames from the start to the end of the trajectory.

If you want to analyze only a specific part of the trajectory, you can use the `begin`, `end`, and `step` options to control the time range and frequency of analysis.

- `begin`: Specifies the time (in ps) from which the trajectory will start being read.
- `end`: Specifies the time (in ps) at which the trajectory analysis will stop.
- `step`: Specifies the step size for reading trajectory frames (i.e., how often a frame will be analyzed).

### Example: Calculating coarse-grained order parameters from a specific part of a trajectory

```yaml
structure: system.tpr
trajectory: md.xtc
analysis_type: !CGOrder
  beads: "@membrane"
begin: 100000.0
end: 300000.0
step: 5
output: order.yaml
```

In this example, the analysis starts at time 100,000 ps (100 ns) and ends at time 300,000 ps (300 ns). Every 5th frame of the trajectory will be analyzed.