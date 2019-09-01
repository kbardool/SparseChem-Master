# Introduction

This package provide **fast** and **accurate** machine learning models for biochemical applications.
Especially, we support very high-dimensional models with sparse inputs, *e.g.*, millions of features and millions of compounds.

# Installation

SparseChem depends on **pytorch**, which you have to install first, other dependencies will be installed with the package:

```
pip install -e .
```

# ChEMBL Example
First data has to be downloaded into `examples/chembl`:
```
https://www.esat.kuleuven.be/~aarany/chembl_23_x.mtx
https://www.esat.kuleuven.be/~aarany/chembl_23_y.mtx
https://www.esat.kuleuven.be/~aarany/folding_hier_0.6.npy
```

Then execute training:
```
cd ./examples/chembl
python chembl_train.py
```

## Specifying parameters of the network
Single layer network with `400` hidden:
```
python chembl_train.py \
  --x ./chembl_23_x.mtx \
  --y ./chembl_23_y.mtx \
  --folding ./folding_hier_0.npy \
  --fold_va 0 \
  --batch_ratio    0.02 \
  --hidden_sizes   400 \
  --last_dropout   0.2 \
  --middle_dropout 0.2 \
  --weight_decay   0.0 \
  --epochs         20 \
  --lr             1e-3 \
  --lr_steps       10 \
  --lr_alpha       0.3
```
We use `0.2` dropout and with no weight decay (regularization).
The total of epochs is `20` and learning rate is `1e-3`.
We also add `--lr_steps 10` that means that the after 10 epochs the learning rate is multiplied by 0.3 (`lr_alpha` value).

This should get us to 0.83 average AUC for tasks with 25 positives and 25 negatives.

## Two layer network
To get a two layer network we just add several values to `--hidden_sizes`.
```
python chembl_train.py \
  --x ./chembl_23_x.mtx \
  --y ./chembl_23_y.mtx \
  --folding ./folding_hier_0.npy \
  --fold_va 0 \
  --batch_ratio    0.02 \
  --hidden_sizes   400 400 \
  --weight_decay   1e-5 \
  --last_dropout   0.2 \
  --middle_dropout 0.2 \
  --epochs         20 \
  --lr             1e-3 \
  --lr_steps       10 \
  --lr_alpha       0.3
```
We also modified the weight decay to `1e-5`.

## AUC calculation
The script uses all data for training but AUCs are calculated only on tasks with enough positive and negative examples, default is `25` each.
To instead require at least 50 positives and 50 negatives, add `--min_samples_auc 50`.

## Input folding
The pipeline also provides an option to fold inputs to a smaller size.
For example, adding `--fold_inputs 20000` folds the inputs to 20,000 dimension.
This is useful for reducing the model size, without hurting the performance too much.

## Running on CPU or other GPUs
The default device is `cuda:0`.
To train the model on CPU just add `--dev cpu` to the arguments.
Similarly, to choose another GPU, we can specify `--dev cuda:1`.


