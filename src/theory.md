# Some theory

Lipid order parameters quantify the degree of ordering in lipid membranes. They can be obtained from both experimental measurements and simulations, making them quite useful for checking force-field accuracy, studying molecular structures, tracking phase transitions and various other stuff.

The order parameter $S$ in **atomistic** systems can be calculated as:

$
S = \dfrac{1}{2} \langle 3 \cos^2 \theta - 1 \rangle
$

where $\theta$ is the angle between the C-H bond vector and the bilayer normal, and the angular brackets represent molecular and temporal ensemble averages. The order parameter for atomistic systems is usually reported as the negative of this calculated value, $-S$ (often denoted as $-S_{CH}$ or $-S_{CD}$). Higher positive values of $-S$ indicate more ordered membrane structures.

In **coarse-grained** systems, the order parameter can be calculated using the same equation. However, here $\theta$ corresponds to the angle between the vector connecting two consecutive beads of the lipid and the bilayer normal. Using this definition, the order parameter $S$ (sometimes denoted as $S_{CC}$) increases as the membrane structure becomes more ordered. For coarse-grained systems, the order parameter is usually reported directly as $S$ to facilitate comparison with the atomistic $-S$ (which follows the same trend).

In **united-atom** systems, the situation is more complex because we aim to calculate order parameters for C-H bonds, but the positions of hydrogen atoms are not *a priori* known. To determine the order parameters, the positions of hydrogen atoms can either be explicitly predicted or implicitly derived during the calculation of lipid order parameters (see [Piggot et al.](https://pubs.acs.org/doi/10.1021/acs.jctc.7b00643) for more details). `gorder` employs the former approach, explicitly predicting the positions of hydrogen atoms using the same method as the NMR Lipids-associated [buildH](https://github.com/patrickfuchs/buildH) tool. Once the hydrogen positions are predicted, the order parameters are calculated in the same manner as for atomistic systems.