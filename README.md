## Self-correcting Bayesian Optimization (SCoreBO) repository for ICML 2023.

This repository makes use of both BOTorch and Ax as submodules, as well as Hydra to run experiments.

#### TO RUN:
0. Retrieve the necessary repositories:     
```git clone https://github.com/scorebo/scorebo.git --recurse```
1. Install the conda environment:   ```conda env create -f env.yml```
2. Add the necessary paths:     ```source setup.sh```
3. cd into botorch and start running experiments. All the necessary (fully bayesian) models are added into models/fully_bayesian.py, and the acquisition functions in aquisition/active_learning.py, as well as some additional utility in Ax. To simply run SCoreBO (with joint conditioning, Hellinger distance) on Branin, run
```python benchmarking/learning_run.py```
and the output can be found in results/test. To run SAL on an active learning benchmark, instead run

```python benchmarking/learning_run.py model=fb_bal_slice algorithm=sal_ws benchmark=gramacy2```

The various aailable arguments are (active learning-based benchmarks/models/algos first, naming differences in branin and hartmann6 specify the AL/BO noise levels):
```benchmark: higdon, gramacy1, gramacy2, ishigami, active_branin, active_hartmann6, gp_4dim, botorch_branin, botorch_rosenbrock2, botorch_rosenbrock4, botorch_hartmann3, botorch_hartmann4, botorch_hartmann6, hpo_blood, hpo_segment```

To run the MLP tuning tasks, you first need to install [HPOBench](https://github.com/automl/HPOBench). Then, these tasks should run like the rest.

Switch models (and hyperparameter priors)
model choices: 
```fb_bal_slice```: Slice sampling, used for AL tasks for speed.
```fb_scorebo_nuts_albo```: NUTS & [AL prior](https://arxiv.org/abs/2205.10186) through Pyro
```fb_scorebo_nuts_bad```: NUTS & poor hyperparameter prior
```fb_scorebo_nuts```: NUTS & BOTorch prior
The number of samples, warmup and thinning are changed by entering ```model.num_samples=XX```, ```model.num_samples=YY``` or ```model.thinning=ZZ``` as additional command line arguments.


```algorithm: sal_ws, al_bqbc, al_qbmgp, al_balm, scorebo_j, gibbon, jes, nei```

The complete list of arguments is available in configs/algorithm. Alternatively, the full list of options (```seed```, ```experiment_group``` etc.) appears when entering an incorrect options argument.


### REFERENCES:
Balandat, M., Karrer, B., Jiang, D. R., Daulton, S., Letham, B., Wilson, A. G., and Bakshy, E. Botorch: A framework for efficient monte-carlo bayesian optimization. In Ad- vances in Neural Information Processing Systems, 2020. URL http://arxiv.org/abs/1910.06403.
Eggensperger, K., M  ̈uller, P., Mallik, N., Feurer, M., Sass, R., Klein, A., Awad, N., Lindauer, M., and Hutter, F. HPOBench: A collection of reproducible multi-fidelity benchmark problems for HPO. In Thirty-fifth Conference on Neural Information Processing Systems Datasets and Benchmarks Track (Round 2), 2021. URL https:// openreview.net/forum?id=1k4rJYEwda-.
Riis, C., Antunes, F. N., H  ̈uttel, F. B., Azevedo, C. L., and Pereira, F. C. Bayesian active learning with fully bayesian gaussian processes. arXiv preprint arXiv:2205.10186, 2022.
