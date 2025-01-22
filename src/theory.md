# Some theory

Lipid order parameters quantify the degree of ordering in lipid membranes. They can be obtained from both experimental measurements and simulations, making them quite useful for checking force-field accuracy, studying molecular structures, tracking phase transitions and various other stuff.

The order parameter $S$ in **atomistic** simulations can be calculated as:

$
S = \dfrac{1}{2} \langle 3 \cos^2 \theta - 1 \rangle
$

where $\theta$ is the angle between the C-H bond vector and the bilayer normal, and the angular brackets represent molecular and temporal ensemble averages. The order parameter for atomistic systems is usually reported as the negative of this calculated value, $-S$ (often denoted as $-S_{CH}$ or $-S_{CD}$). Higher positive values of $-S$ indicate more ordered membrane structures.

For **coarse-grained** systems, the order parameter can be calculated using the same equation. However, here $\theta$ corresponds to the angle between the vector connecting two consecutive beads of the lipid and the bilayer normal. Using this definition, the order parameter $S$ (sometimes denoted as $S_{CC}$) increases as the membrane structure becomes more ordered. For coarse-grained systems, the order parameter is usually reported directly as $S$ to facilitate comparison with the atomistic $-S$ (which follows the same trend).