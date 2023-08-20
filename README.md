## Self-correcting Bayesian Optimization (SCoreBO) through Bayesian Active Learning official repository for NeurIPS 2023.

This repository makes use of BOTorch[1], Gpytorch[2], Ax as submodules, as well as Hydra to run experiments, since each of these repositories contain necessary modifications to make all experiments runnable. All experiments are run with seed 1-MAX_REPS, where the number of max repetitions is specified for each benchmark in each caption in the paper.

### TO RUN:
0. Retrieve the necessary repositories:     
```git clone https://github.com/scorebo/scorebo.git --recurse-submodules```
1. ```cd scorebo```
2. Install the environment:   
```pip install -r requirements.txt```
 (python 3.9 is the only version tested for support)
3. Add the necessary paths to PYTHONPATH:     
```source add_paths.sh```
4. ```cd botorch``` and start running experiments. All the necessary (fully bayesian) models are added into models/fully_bayesian.py, and the acquisition functions in aquisition/active_learning.py, as well as some additional utility in Ax. 

##### QUICK START
To simply run SCoreBO (Hellinger distance) on Branin with a lognormal hyperparameter prior, run

```python benchmarking/learning_run.py```

and the output can be found in results/test. 

```Guess values``` = The best guess, used to calculate inference regret.

```True Eval``` = The (noiseless!) value on the benchmark, used to calculate simple regret.

To run SAL on an active learning benchmark, instead run

```python benchmarking/learning_run.py model=bal_lognormal algorithm=sal_ws benchmark=gramacy2 +AL=1```

where ```+AL=1``` enables the AL metrics, RMSE and (negative) MLL to be tracked. Then, the output ```results.csv```-file will additionally include:

```RMSE``` = Root mean squared error on the SOBOL-sampled validation set.

```MLL``` = The negative marginal log likelihood on the SOBOL-sampled validation set.

All runs additionally include a ```...hps.json``` which contains all the hyperparameter sets for each problem, per iteration.

The various available arguments are (active learning-based benchmarks/models/algos first, naming differences in branin and hartmann6 specify the AL/BO noise levels):

```benchmark: higdon, gramacy1, gramacy2, ishigami, active_branin, active_hartmann6, gp_4dim, branin, rosenbrock2, rosenbrock4, hartmann3, hartmann4, hartmann6, gp_8dim, gp_2_2_2dim, gp_2_2_2_2_2dim, cosmo, ackley4_25, hartmann6_25, lasso_dna, pd1_wmt, pd1_lm1b, pd1_cifar```

##### GP SAMPLE TASKS.
First, create the csv files which contain the fourier features for each sample task. This is done by (inside the botorch directory), running:

```python benchmarking/gp_sample/gen.py [num_tasks (int)] [dimensionality (int)] [matern (bool/int)] [lengthscales (list(int))]```

So, to recreate the 8-dim tasks in the paper (with different RNG), run:
```python benchmarking/gp_sample/gen.py 25 8 1 0.1 0.313 0.313 1 1 31.3 31.3 31.3```

or for the 2-dim needed for the additive tasks, run:
```python benchmarking/gp_sample/gen.py 25 2 0 0.5 0.5```


##### OPTIONS
###### Model Options

Switch models (and hyperparameter priors)
model choices: 

``` additive ```: AddGP model using additive decompositions

``` bal_lognormal ```: Model used for BAL experiments, LogNormal prior with no mean hyperparameter

``` bal_lognormal_no_outputscale ```: Model used for appendix BAL experiments, LogNormal prior with no outputscale or mean hyperparameter

``` bo_lognormal ```: Model used for BO experiments, LogNormal prior with mean hyperparameter

``` botorch ```: BoTorch Prior, used for 8-dim GP experimetns

``` saasbo ```: SAASBO Prior, used for high-dimensional experiments.

``` bo_warping ```: HEBO Warping Prior, used for PD1 tasks.

The number of samples, warmup and thinning are changed by entering ```model.num_samples=XX```, ```model.num_samples=YY``` or ```model.thinning=ZZ```. The pyro MC progress bar can be turned of with  ```model.disable_progbar=True``` as additional command line arguments.


###### Algorithm Options

```algorithm: sal_ws, sal_hr, bqbc, qbmgp, balm, scorebo_j_hr, scorebo_j_ws, gibbon, jes, nei```. Additional algorithm options can be found within each algorithm's ```yaml``` file. To change to MC estimation for SAL/SCoreBO, simply append the algorithm hydra config:

```++algorithm.acq_kwargs.estimate=MC```

Additional options can be found in each algorithm's .yaml-file.

###### Benchmark Options
The ```noise_std, num_iters, num_init``` of each benchmark can also be altered by entering ```benchmark.example_argument = value```, such as

```benchmark=hartmann3 benchmark.noise_std = 0.0 benchmark.num_init=10 benchmark.num_iters=40``` to run noiseless Hartmann-3 for 40 iterations with 10 initial DoE samples + the remaining 30 samples as BO. 


The complete list of arguments is available in configs/algorithm. Alternatively, the full list of options (```seed```, ```experiment_group``` etc.) appears when entering an incorrect options argument.


##### COSMOLOGICAL CONSTANTS.
The cosmological constants task does require a working fortran f90 installation, and the Intel Fortran Compiler. The cosmological constants benchmark has been altered from the TurBO [4] bounds and one parameter has been removed entirely, to avoid infeasible regions with exceedingly long runtime. The bounds that are used are:
```
ombh2=[0.01, 0.08]
omch2=[0.01, 0.25]
omk=[0.01, 0.25]
hubble=[52.5, 100]
temp_cmb=[2.7, 2.8]
helium_fraction=[0.2, 0.3]
massless_neutrinos=[2.9, 3.09]
scalar_amp=[1.5e-9, 2.6e-8]
scalar_spectral_index=[0.72, 2.7]
RECFAST_fudge=[0, 100]
RECFAST_fudge_He=[0, 100]
```

For licensing reasons, we cannot provide the full code for running the benchmark. We have, however, left in the script for calling the likelihood (including the bounds on the benchmark) in ```benchmarking/cosmo_task.py```.


##### PD1 benchmarks with input warping.
The PD1 benchmarks require the download of datasets from the mf-prior-bench repository. The extensive how-to is available in that repo's readme, but in short:

Go into the mf-prior-bench repository, and download the required datasets for PD1:
```python -m mfpbench.download```

After doing so, ensure that the surrogates (two JSON-files per benchmark) can be found in mf-prior-bench/data/surrogates. If you run into issues, we kindly refer you to the README of the mf-prior-bench repository.
The name of the PD1 tasks are ```pd1_wmt, pd1_lm1b, pd1_cifar```


### REFERENCES:
1. Balandat, M., Karrer, B., Jiang, D. R., Daulton, S., Letham, B., Wilson, A. G., and Bakshy, E. Botorch: A framework for efficient monte-carlo bayesian optimization. In Ad- vances in Neural Information Processing Systems, 2020. URL http://arxiv.org/abs/1910.06403.

2. Gardner, J. R., Pleiss, G., Bindel, D., Weinberger, K. Q., and Wilson, A. G. Gpytorch: Blackbox matrix-matrix gaussian process inference with GPU acceleration. In Advances in Neural Information Processing Systems, 2018.

3. Riis, C., Antunes, F. N., H  ̈uttel, F. B., Azevedo, C. L., and Pereira, F. C. Bayesian active learning with fully bayesian gaussian processes. arXiv preprint arXiv:2205.10186, 2022.

4. https://github.com/uber-research/TuRBO
