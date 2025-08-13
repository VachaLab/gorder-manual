# How to read this manual

> Too lazy or too busy to read the manual? [Click here](raw.html) for an LLM-friendly version of it. You can simply download this raw manual, upload it into your LLM of choice and ask for guidance if you get stuck. **Do not trust it blindly though, LLMs often miss important things!**

This is a manual for the `gorder` tool—a command-line (CLI) application, Python package, and Rust crate for calculating lipid order parameters from molecular simulations. Most of the manual focuses on the CLI application.

The contents of this manual are shown in the panel on the left side of the page.

In the **Introduction** section, we discuss [why `gorder` exists](introduction.md) and [what lipid order parameters even are](theory.md). Feel free to skip this section.

In the **Beginner** section, we describe [how to install `gorder`](installation.md) and run basic analyses of [atomistic](aaorder_basics.md), [coarse-grained](cgorder_basics.md), and [united-atom](uaorder_basics.md) systems. If you are only interested in, for example, atomistic systems, you can skip the pages dedicated to coarse-grained and united-atom analyses.

In the **Advanced** section, we discuss various features of `gorder`, such as calculating order parameters [for individual leaflets](leaflets.md), constructing [order parameter maps](ordermaps.md), estimating [analysis errors](errors.md), calculating order parameters [in a specific part of the membrane](geometry.md), and analyzing simulations using [dynamically estimated membrane normals](membrane_normal.md#dynamic-membrane-normal). The individual pages are quite independent, so you can skip directly to the ones you need.

In the **Expert** section, we cover more advanced features of `gorder`, which mostly provide greater control over the analyses. This includes [manual lipid assignment to leaflets](manual_leaflets.md), [manual specification of membrane normals](manual_normals.md), and [ignoring periodic boundary conditions](no_pbc.md). You will most likely not need these features.

In the **APIs** section, we briefly explain how to use `gorder` directly as a [Python package](python_api.md) or a [Rust crate](rust_api.md). These topics also have their own dedicated manuals ([Python](https://ladme.github.io/pygorder-docs/), [Rust](https://docs.rs/gorder/latest/gorder/)). The CLI version of `gorder` is fully featured; you do not need to use the APIs unless you want direct integration with your code.

In the **Meta** section, we discuss the [limitations of `gorder`](limitations.md) (**you should read this page!**), where to [send your feedback](feedback.md), and [how to cite `gorder`](citing.md) if you use it in a scientific project.