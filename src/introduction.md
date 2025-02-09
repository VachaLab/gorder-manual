# What is gorder?

`gorder` is a command-line tool that aims to provide a comprehensive, reliable, simple, and fast way to calculate atomistic and coarse-grained lipid order parameters from Gromacs simulations.

The primary (and possibly naïve) goal of `gorder` is to completely replace the use of [`gmx order`](https://manual.gromacs.org/current/onlinehelp/gmx-order.html) for atomistic and coarse-grained systems. `gmx order` is a tool still widely used in the Gromacs community despite its tedious usage and the fact that it sometimes produces completely [incorrect results](https://pubs.acs.org/doi/10.1021/acs.jctc.7b00643).[^1]

Apart from calculating the order parameters correctly, `gorder` also offers many additional features, such as reporting order parameters for individual C-H bonds, error estimation, leaflet classification, geometric selection, or construction of ordermaps. `gorder` can also calculate order parameters for non-planar membranes such as tubes or vesicles.

> `gorder` is written in [Rust](https://www.rust-lang.org/) and is also available as a [Rust crate](https://docs.rs/gorder/latest/gorder). If you want to see the code (or comparison with other programs), visit the [`gorder` GitHub repository](https://github.com/Ladme/gorder).

***
[^1]: Even [Gromacs developers say](https://manual.gromacs.org/2024.1/onlinehelp/gmx-order.html#known-issues) that you should not use `gmx order`.