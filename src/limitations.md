# Limitations

While `gorder` aims to provide accurate and reliable results, there are some known limitations and scenarios where it may not be suitable. The current limitations are as follows:

1. **`gorder` cannot read TPR files generated with Gromacs versions older than 5.1.**
   - **Why is this the case?** This limitation stems from the [`minitpr`](https://github.com/Ladme/minitpr) crate used to parse TPR files. The TPR file format evolves constantly, and supporting all ancient Gromacs versions is simply not feasible.
   - **What can you do if your TPR file is this old?** In most cases, the solution is simple: generate a TPR file using a newer version of Gromacs. Since `gorder` only needs the system topology, you can use `gmx grompp` with any MDP file to create an updated TPR file. If generating a new TPR file is not an option for you, you can instead provide a PDB file with a connectivity section or a GRO file along with a bonds file (see [Using other input file formats](other_input.md)).
   - **Will this be addressed in the future?** Older Gromacs versions will likely **never** be fully supported.

2. **`gorder` cannot analyze simulation boxes that are *not orthogonal* (i.e., boxes that are not rectangular cuboids).**
   - **Why is this the case?** This limitation arises from the [`groan_rs`](https://github.com/Ladme/groan_rs) crate, which provides only partial support for non-orthogonal simulation boxes. Handling periodic boundary conditions in such boxes is complex and currently not fully implemented.
   - **What can you do if your box is not orthogonal?** Unfortunately, you will need to use alternative software for non-orthogonal boxes. 
   - **Will this be addressed in the future?** `gorder` is unlikely to ever *fully* support non-orthogonal simulation boxes. This is because non-orthogonal simulation boxes are just not very common in membrane simulations. `gorder` will eventually support an option to disable periodic boundary condition handling, allowing for some analysis of these simulations. This would however require preprocessing of the data (e.g., ensuring molecules are whole) before using `gorder`.

3. **`gorder` may not fully support systems without periodic boundary conditions applied in all three dimensions.**
   - **Why is this the case?** This limitation again arises from the [`groan_rs`](https://github.com/Ladme/groan_rs) crate.
   - **What can you do if your system lacks PBC in some dimensions?** In most cases, no immediate action is required, but note that such systems have not been tested at all, and artifacts may occur. You can try making the molecules whole within the simulation box to mitigate potential issues and check for any changes.
   - **Will this be addressed in the future?** While `gorder` may never fully support systems without PBC in all dimensions, future updates are expected to include an option to disable PBC handling. This would enable analysis of these systems with some small additional friction.


