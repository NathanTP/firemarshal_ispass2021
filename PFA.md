This repository acts as a stable commit for reproducing experiments reported in
"FireMarshal: Reproducible Software Workload Management" published in ISPASS
2021. Note that this is an older version of FireMarshal than was reported in
that paper.

It is also a good reference point for understanding the PFA. A report on
an older version of this project is available as "Enabling Efficient and
 Transparent Remote Memory Access in Disaggregated Datacenters" published as
Technical Report No. UCB/EECS-2019-154.

# Prerequisites
This experiment is designed in conjunction with a particular commit of firesim.
It is best if you clone that repository and use the version of firesim-software
(a.k.a. FireMarshal) submoduled there: firesim:
git@github.com:firesim/firesim.git @ 072f7197814133c6c11d3831f61b0bacb36576b3

Along with a compatible FireSim and hardware infrastructure, this repository
contains the toolchain used in the reported experiments. More recent toolchains
will likely also work and should have little effect on this particular
experiment. However, it is sensitive to the version of QEMU due to changes in
shutdown logic. See the firesim documentation for toolchain instructions.

# Quickstart
First checkout submodules

    git submodule update --init --recursive

Then setup the PFA experiment repository

    ./workloads/pfa-exp/install.sh

You can now test a workload in QEMU. Since QEMU lacks a functional model of the
PFA, this workload is configured to emulate PFA behavior in the kernel:

    ./marshal build workloads/pflat-em-pfa.json
    ./marshal launch workloads/pflat-em-pfa.json

Spike has a functional model of the PFA and we can run an experiment against this
model. Since the model is only functional, the results won't be reasonable, but
the experiment should run correctly:

    ./marshal -i build workloads/pflat-real-pfa.json
    ./marshal -i test -s workloads/pflat-real-pfa.json

Finally, we can run a test in FireSim. This step assumes that you are using
firesim-software/ as a submodule of FireSim. install.sh already configured
FireSim to run PFA experiments but we haven't yet generated a FireSim
compatible workload description, we'll do that now:

    ./marshal install workloads/pflat-real-pfa.json
    ./marshal install workloads/pflat-em-mb.json

You can now run the experiment in FireSim (see the FireSim documentation for
details: https://docs.fires.im/en/1.4.0/).

# Reproducing Figures from ISPASS 2021 Paper
Assuming you have followed the quickstart, FireSim should be configured to run
a 2-node experiment using the real PFA.
    * Run firesim as described in the documentation. It will report an output
      directory which will contain the results of this experiment. e.g.:
    FireSim Simulation Exited Successfully. See results in:
/home/centos/firesim_ispass/deploy/results-workload/2021-02-09--17-17-44-pflat-real-pfa/

    * Copy results.csv to a results directory of your choosing (e.g.
      ~/pfa\_res) and name it "pfa\_pflat\_1610.csv".
    * Replace firesim/deploy/runtime.ini with
      workloads/pfa-exp/fs-configs/baseline\_runtime.ini and repeat the experiment
      (this configuration is set to run the pure-software baseline). This time,
      name the results file "sw\_pflat\_1610.csv".
    * You can now launch the jupyter notebook in
      workloads/pfa-exp/pfa-results/ispass.ipynb. You will need to change the
      path in the "Load Raw Results" section to your output directory.

## Determinism
This experiment was performed on an older version of both FireSim and
FireMarshal. While FireMarshal provides deterministic and reproducible workload
builds, this version of FireSim has a few sources of non-determinism. Your
results should be similar and follow similar patterns, but the exact numbers
may not match those in the paper.
