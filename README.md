This repository acts as a stable commit for reproducing experiments reported in
"FireMarshal: Reproducible Software Workload Management" published in ISPASS
2021.

# Experiments
## PFA
This experiment is described in section IV.A of the paper. It is based on an
older version of firesim and FireMarshal. The main submodule for this
experiment is firesim-PFA/ and more detailed instructions are available in
PFA.md.

## SPEC
This workload is described in section IV.B of the paper. The SPEC workload is
(as of writing) included in the chipyard repository as a standard workload
(chipyard/software/spec2017). We have submoduled the version of chipyard used
in the paper here. See SPEC.md for more detailed reproduction instructions.

# Requirements
These experiments were run on the recommended host system as described in the
Chipyard documentation ([chipyard online
docs](https://chipyard.readthedocs.io/en/1.3.0/Chipyard-Basics/Initial-Repo-Setup.html)
the documentation may also be viewed locally from the included chipyard-SPEC/docs
directory). For experiments on FireSim, see the [chipyard firesim
documentation](https://chipyard.readthedocs.io/en/1.3.0/Simulation/FPGA-Accelerated-Simulation.html#firesim-sim-intro)
for initial setup instructions.

We also rely on a number of pre-built FireSim hardware images (AGFIs) that may
or may not be available in the future. Hardware image reproducibility is out of
scope for FireMarshal.

# Reporting Issues
Please report any difficulties in reproducing these experiments through the
github issue tracker. If the issues can be reproduced on the dev branches of
either tool, you may also report bugs on their github pages. We are actively
developing both FireMarshal and FireSim and welcome feedback.

Note that the PFA experiments in particular are based on
older versions of both tools and may have bugs or difficulties that are
now fixed. We would still like to hear about any issues in these instructions
though.
