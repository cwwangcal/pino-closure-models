markdown 效果实验

# PINO for long-term stats in chaotic dynamics

This repository contains the code implementation for the paper **Beyond Closure Models: Learning Chaotic-Systems via Physics-Informed Neural Operators**. The paper is available at [arxiv.org/abs/2408.05177](https://arxiv.org/abs/2408.05177).

## Introduction

Our paper contains experiments on three chaotic dynamics, 1D Kuramoto–Sivashinsky, 2D Kolmogorov Flow (Navier-Stokes) with small (100) and challenging ($1.6\times 10^4$) Reynolds number.

For each dynamics (experiment), the codes consist of the following parts.
1. Numerical Solver (for data generation).
1. Training PINO Model (three steps as described in the paper).
2. Evaluation (estimating long-term statistics and visulizing the results)
3. Baseline methods.

## KS equation
**Repo Structure**
```
ks/
│
├── ks_train_pino.py, ks_train_pino_2.py, ks_train_pino_3.py  # Main entry point for training the model, corresponding to the three steps in our algorithm.
├── solver /          # Numerical Solver for data generation.
├── data/            # Codes for data preprocessing and datasets.
│     ├── stat_save/   # Statistics estimated with different methods.
├── evaluation/        # Codes for evaluations.
├── model_save/        # The model after training.
├── fig_save/         # Visualizations of the experiment results.
├── ../neuralop_base/    # Neural Operator Model.
└── ../config/ks_*.yaml    # Configuration files for experiments.
```

### Data generation
```
cd ks/solver
python KS_solver.py
```
Control domain size, viscosity coefficient,  the spatio and temporal grid size, the number of trajectories, and the time interval of saving snapshots in `ks/solver/ParaCtrl.py`

After generating the data, move the dataset file to `ks/data` and replace the links in Line 15,19,28,30 in  `ks/data_dict.py` with the filename of the dataset.

### Training
Before training, set wandb project name and username in configuration files (../config/ks_*.yaml)
```
wandb:
    project: # add project name here
    entity:  # add your username here
```

Train the PINO model with
```
cd ks/
python 
ks_train_pino.py   # Supervised-learning with CGS data.
ks_train_pino_2.py  # Supervised-learning with CGS and some FRS data.
ks_train_pino_3.py  # Training with PDE loss.
```

### Evaluation
Before testing the model, one need to have `ks/data/ks_stat_uv_emp_range.pt` (used for computing total variation error), `ks/data/ks_128_1500_random_init.pt` (used as random initialization for evaluation experiments) ready. If they were not there, the former one is generated by running `python range_of_emp_measure.py`, and the second one is generated by saving $t=0$ snapshots from the solver code.

To apply the trained neural operator in coarse-grid simulations, run
```
cd ks/evaluation
python station.py
```

To visualize the results, run `python final_compare_stat.py`. Codes related to visulization and computing error are two functions, `plot_all_stat` and `save_all_err` in `evaluation/plot_stat_new.py`.

### Baseline Methods

## NS equation (small Re)

## NS equation (high Re)

## Contact

May you have any questions on our work or implementation, feel free to reach out to [chuweiw at caltech dot edu](chuweiw@caltech.edu).

## Citation
If you find this repository useful, please consider giving a star ⭐ and cite our paper.
```
@article{wang2024beyond,
  title={Beyond Closure Models: Learning Chaotic-Systems via Physics-Informed Neural Operators},
  author={Wang, Chuwei and Berner, Julius and Li, Zongyi and Zhou, Di and Wang, Jiayun and Bae, Jane and Anandkumar, Anima},
  journal={arXiv preprint arXiv:2408.05177},
  year={2024}
}
```