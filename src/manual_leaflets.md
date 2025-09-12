# Manual lipid assignment and export

This chapter describes two advanced features related to leaflet classification: manual assignment and export.

### Manually assigning lipids to leaflets
If you prefer not to use the automatic leaflet classification (described in [Order parameters for individual leaflets](leaflets.md))—for example, due to your system's complex geometry or a lack of confidence in `gorder`'s assignment—you can manually specify the leaflet occupied by each lipid molecule for each analyzed frame.

There are two ways to manually assign lipids to membrane leaflets: using [NDX files](leaflets_ndx.md) or a ["leaflet assignment file"](leaflets_assignment_file.md).

### Exporting leaflet assignment data
If you are using the built-in [automatic leaflet classification](leaflets.md), you may want to access the leaflet assignment results—for example, to verify that the assignment is correct or to reuse the lipid assignment in additional analyses. Instructions for doing this can be found in this [section of the manual](leaflets_export.md).