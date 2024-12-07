# What is gorder?

`gorder` is a command-line tool that aims to provide a comprehensive, reliable, simple, and fast way to calculate atomistic and coarse-grained lipid order parameters from Gromacs simulations.

The primary (and possibly naïve) goal of `gorder` is to completely replace the use of [`gmx order`](https://manual.gromacs.org/current/onlinehelp/gmx-order.html) for atomistic and coarse-grained systems. `gmx order` is a tool still widely used in the Gromacs community despite its tedious usage and the well-known fact that it produces incorrect results for many systems [[1](https://pubs.acs.org/doi/10.1021/acs.jctc.7b00643)].

`gorder` also offers many additional features, such as leaflet classification or construction of ordermaps.

***

`gorder` is written in [Rust](https://www.rust-lang.org/) and is also available as a [Rust crate](https://docs.rs/gorder/latest/gorder).