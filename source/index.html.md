---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python--tensorflow: TensorFlow
  - python--keras: Keras
  - python--pytorch: Pytorch
  - java--spark: Spark

toc_footers:
  - <a href='https://wandb.ai'>Login to wandb</a>

includes:
  - logging
  - sweep


search: true
---

# Introduction
```shell
# Install wandb
pip install wandb
```

WandB is a productivity tool for building machine learning models.  We help you
track everything you do, visualize your results and automate large-scale hyperparameter search.



# Getting started

```shell
# Initialize wandb in the root directory of your project
wandb init
```

> Near the top of your code add initialization code:

```python
# Inside my model training code
import wandb
run = wandb.init()

# Run my complicated training loop...
```

> Modify your command to run training

```shell
# The old way to run your training script (still works, but no magic)
python learn.py
```

```shell
# The new way to run your training script (saves logs, and so much more...)
wandb run learn.py
```

It's easy to configure WandB to work with one of your projects.  

Sign up for an account at <https://wandb.ai>

You will be prompted for a team name and a project name.  This will create a
wandb directory that contains a settings file with your entity name and
project name.

Now you every time you run your training script with wandb, a new record will
be added to https://wandb.ai/$ENTITY_NAME/$PROJECT_NAME.  Your training logs
will be saved along with a snapshot of your latest commit.

<aside class="notice">
You can always rerun *wandb init* to change your project's settings.
</aside>
