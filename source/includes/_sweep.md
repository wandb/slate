# Hyperparamer Search


## Running a Parameter Sweep

To run a parameter sweep:

1. Initialize your project to use wandb in the cloud.
2. Create a sweep.yaml file specified below, which specified your training script, your parameter
ranges and the search strategy.
3. Initialize your sweep, which gives you a SWEEP_ID and a url to track all of
your runs.
4. Run wandb agent, which will get a set of parameter names and arguments from the
wandb server and then run your training script with the parameters as arguments.  You can
run as many agents as you like.

> Start a search from your projects root directory

```shell
wandb init # If you haven't already initialized your project
wandb sweep sweep.yaml # returns SWEEP_ID.
```

> Start an agent

```shell
wandb agent SWEEP_ID
```


## Sweep.yaml File
> Example sweep.yaml

```yaml
description: random sweep for my little cnn

# Training script to run
program: cnn.py  

# Method can be bayes, random, grid
method: bayes

# Metric to optimize
metric:
  name: val_loss
  goal: minimize

# Should we early terminate runs
early_terminate:
  type: envelope

# Parameters to search over
parameters:
  learning-rate:
    min: 0.001
    max: 0.1
  optimizer:
    values: ["adam", "sgd"]
  dropout:
    min: 0.01
    max: 0.5
  epochs:
    value: 30
```

### Method
Values | Meaning
------ | -------
grid | Grid Search - Will iterate over all possible sets of values in parameters.
random | Random Search - Will choose random sets of values
bayes | Bayesian Optimization - Uses a gaussian process to model the function and then chooses parameters to optimize probability of improvement

### Early Terminate
Early termination is a strategy to speed up hyperparameter search by killing off runs that
appear to have lower performance than successful training runs.

Values | Meaning
------- | -------
envelope | Use an envelope method for early termination/
Not Specified | Don't do early termination.

### Parameters

The parameters dictionary specifies the ranges of configuration parameters.

Values | Meaning
------ | -------
min: (float) max: (float) | Continuous values between min and max
min: (int) max: (int) | Integers between min and max
values: [a, b, c] | Discrete values
value: | A constant
