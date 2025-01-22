# Limitations

While `gorder` aims to provide accurate and reliable results, there are some known limitations and scenarios where it may not be suitable. The current limitations are as follows:

1. **`gorder` cannot read TPR files generated with Gromacs versions older than 5.1.**
   - **Why is this the case?** This limitation stems from the [`minitpr`](https://github.com/Ladme/minitpr) crate used to parse TPR files. The TPR file format evolves constantly, and supporting all ancient Gromacs versions is simply not feasible.
   - **What can you do if your TPR file is this old?** In most cases, the solution is simple: generate a TPR file using a newer version of Gromacs. Since `gorder` only needs the system topology, you can use `gmx grompp` with any MDP file to create an updated TPR file. If generating a new TPR file is not an option for you, you can instead provide a PDB file with a connectivity section or a GRO file along with a bonds file (see [Using other input file formats](other_input.md)).
   - **Will this be addressed in the future?** Older Gromacs versions will likely **never** be fully supported.

2. **`gorder` only fully supports systems with orthogonal simulation boxes and periodic boundary conditions applied in all three dimensions.**
   - **Why is this the case?** This limitation arises from the [`groan_rs`](https://github.com/Ladme/groan_rs) crate, which provides only partial support for non-orthogonal simulation boxes. Handling periodic boundary conditions in such boxes is complex and not fully implemented at present.
   - **What can you do if your box is not orthogonal?** You must instruct `gorder` to [ignore periodic boundary conditions](no_pbc.md). Then, provide a trajectory where the lipid molecules are whole, and perform the analysis as usual.
   - **What can you do if your system is not periodic in some dimensions?** In most cases, there will be no issue; however, such systems have not been tested, and artifacts may occur. You can try [ignoring periodic boundary conditions](no_pbc.md) while ensuring the lipid molecules are whole and verify if the results change.
   - **Will this be addressed in the future?** `gorder` is unlikely to ever fully support non-orthogonal simulation boxes, simply because they are not common in membrane simulations. However, better support for partially periodic systems might be added in the future.