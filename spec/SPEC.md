Instructions to reproduce results in section IV.B of "FireMarshal: Reproducible
Software Workload Management" published in ISPASS 2021

# Overview
This experiment evaluates the Berkeley out-of-order machine (BOOM) core using
two different branch predictors using the SPEC2017 benchmark suite. The
benchmark will be run using the chipyard framework. As a hardware development
framework, Chipyard is designed to package all dependencies for a hardware
project including RTL, toolchain, and FireMarshal workloads. 

Other than the hardware configurations, all sources are from released versions
of chipyard. The spec/ directory has all artifacts needed to reproduce this
experiment and the instructions assume you are in this directory.

# Setup
## Chipyard
We have included the correct version of chipyard as a submodule which should be
used for all experiments. These experiments were run on the recommended host
system as described in the Chipyard documentation ([chipyard online
docs](https://chipyard.readthedocs.io/en/1.3.0/Chipyard-Basics/Initial-Repo-Setup.html)
the documentation may also be viewed locally from the included
`chipyard/docs` directory). For experiments on FireSim, see the [chipyard
firesim
documentation](https://chipyard.readthedocs.io/en/1.3.0/Simulation/FPGA-Accelerated-Simulation.html#firesim-sim-intro)
for initial setup instructions.

In particular, make sure you source `env.sh` to get the correct toolchain and marshal version.

## SPEC
This experiment relies on SPEC2017. Unfortunately, this benchmark is
closed-source and cannot be included in a straightforward way. You will need to
acquire the SPEC2017 source and install it per their instructions. Once you
have it installed, set the `$SPEC_DIR` environment variable to your install path.

# Configuring and Building the Hardware
If you wish to run FireSim experiments, you will need to generate the
experimental hardware for the GShare branch predictor (TAGE is already the
default and prebuilt with Chipyard releases). The included chipyard branch
already has the new hardware configs, but we need to tell FireSim about these
new configs:

    cp config_build_recipes.ini chipyard/sims/firesim/deploy/config_build_recipes.ini
    cp config_build.ini chipyard/sims/firesim/deploy/config_build.ini

You are now ready to build the hardware images by following the [FireSim
Documentation](https://docs.fires.im/en/1.10.0/Building-a-FireSim-AFI.html) to
build the `[firesim-boom-2bpd]` configuration.

# Building the FireMarshal Workloads
The paper uses the reference dataset of the intspeed suite. This is quite
long-running and only needed to fully reproduce the reported results. We will
use `spec17-intspeed-test.json` in these instructions for users that just want to
try things out. To reproduce the paper results, use `spec17-intspeed.json`
instead (including in `config_runtime.ini` in firesim).

To build the FireMarshal workloads, navigate to the `chipyard/software/spec2017`
directory and build the experiment workload:

    marshal build ./marshal-configs/spec17-intspeed-test.json

To make sure everything built correctly, and to test the post-run-hook, you can
run all jobs in a single launch command (warning, this may take a while):

    marshal launch -j '' marshal-configs/spec17-intspeed-test.json

> **note:** If you launch any job in the spec workloads multiple times, the
> post-run-hook will not work correctly. You will need to clean the images
> after each run. This is a known bug that was present at the time of
> publication.

# Running in FireSim
We can now install the workload to FireSim:

    marshal install ./marshal-configs/spec17-intspeed-test.json

Next, configure FireSim to run the benchmark against our TAGE configuration:

    cp runtime_tage.ini chipyard/sims/firesim/deploy/config_runtime.ini

Run the benchmark in FireSim. In the output directory, you should see a
results.csv and results.pdf containing the experiment outputs. Copy these to a
results directory.

Repeat for the GShare setup (runtime_gshare.ini).

The combined figure can be generated using compare_results.py

    ./chipyard/software/spec17/compare_results.py -s intspeed -d test -n gshare,tage results/gshare.csv results/tage.csv
