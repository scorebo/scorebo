## Self-correcting Bayesian Optimization (SCoreBO) repository for ICML 2023.

This repository makes use of both BOTorch and Ax as submodules, as well as Hydra to run experiments.

#### TO RUN:
0. Pull the necessary repositories:     
```git pull https://github.com/scorebo/scorebo.git --recurse-submodules```
1. Install the conda environment:   ```conda env create -f env.yml```
2. Add the necessary paths:     ```source setup.sh```
3. cd into botorch and start running experiments. All the necessary (fully bayesian) models are added into models/fully_bayesian.py, and the acquisition functions in aquisition/active_learning.py, as well as some additional utility in Ax. To simply run SCoreBO (with joint conditioning, Hellinger distance) on Branin, run
```python benchmarking/learning_run.py```
and the output can be found in results/test. To run SAL on an active learning benchmark, instead run

```python benchmarking/learning_run.py model=fb_bal_slice algorithm=sal_ws benchmark=gramacy2```

The various aailable arguments are (active learning-based benchmarks/models/algos first):
```benchmark: higdon, gramacy1, gramacy2, ishigami, active_branin, active_hartmann6, gp_4dim, botorch_branin, botorch_rosenbrock2, botorch_rosenbrock4, botorch_hartmann3, botorch_hartmann4, botorch_hartmann6, hpo_blood, hpo_segment```

```model: fb_bal_slice, fb_scorebo_nuts_albo, fb_scorebo_nuts_bad```


```algorithm: sal_ws, sal_js, sal_hr, sal_bc, al_bald, al_balm, al_bqbc, al_qbmgp, scorebo_j, scorebo_j_star, gibbon, jes-e-lb2, nei```

The complete list of arguments is available in configs/algorithm.