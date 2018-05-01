# Logical Factorisation Machines
[![Build Status](https://travis-ci.org/TammoR/LogicalFactorisationMachines.svg?branch=master)](https://travis-ci.org/TammoR/LogicalOperatorMachines)

This package generalises the deprecated [OrMachine](https://github.com/TammoR/ormachine) package.
If you are looking for an implementation of Boolean Matrix Factorisation or Boolean Tensor Factorisation, 
you should use Logical Factorisation Machines with the default model `OR-AND`.

This requires Python 3 and the [numba](numba.pydata.org) package.
The easiest way is to use the [Anaconda Python distribution](https://www.anaconda.com/download).
See [here](https://pypi.python.org/pypi/numba) numba installation instructions.

For installation go to the cloned directory and do `pip install .`.

## Basic usage example

All (optional) steps can be ignored.

```
import lom

# generate toy data
N = 10
D = 5
L = 3
Z = np.array(np.random.rand(N,L) > .5, dtype=np.int8)
U = np.array(np.random.rand(D,L) > .5, dtype=np.int8)
X = aux.lom_generate_data([2 * Z-1, 2 * U-1], model='OR-AND')

# initialise model
orm = lom.Machine()
data = orm.add_matrix(X, fixed=True)
layer = orm.add_layer(latent_size=3, child=data, model='OR-AND')

# initialise factors (optional)
layer.factors[0].val = np.array( 2*(np.random.rand(N, L) > .5) - 1, dtype=np.int8)

# Fix particular entries (1s in fixed_entries matrix) (optional)
layer.factors[1].fixed_entries = np.zeros(layer.factors[1]().shape)
layer.factors[1].fixed_entries[0,:] = 1

# Set priors beta prior on sigmoid(lambda) (optional)
layer.lbda.beta_prior = (10,1)

# Set iid bernoulli priors on factor matrix entries (optional)
layer.factors[1].bernoulli_prior = .1

# Use annealing to improve convergence (optional, not needed in general).
orm.anneal = True
layer.lbda.val = 2.0

# run inference
orm.infer(burn_in_min=100, burn_in_max=1000, no_samples=50)

# to inspect results do 
# [layer.factors[i].show() for i in range(len(factors))]
```