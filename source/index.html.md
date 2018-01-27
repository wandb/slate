---
title: API Reference

language_tabs: # must be one of https://git.io/vQNgJ
  - python--tensorflow: TensorFlow
  - python--keras: Keras
  - python--pytorch: Pytorch

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

Wandb is a tool for people building machine learning models.  

Wandb helps with:

1.  Tracking, saving and reproducing models.  
2.  Visualizing results across models.
3.  Automating large-scale hyperparameter search.

# Getting Started

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

It's easy to configure Wandb to work with one of your projects.  

Sign up for an account at <https://wandb.ai>

You will be prompted for a team name and a project name.  This will create a
wandb directory that contains a settings file with your entity name and
project name.

Now every time you run your training script with wandb, a new record will
be added to https://wandb.ai/$ENTITY_NAME/$PROJECT_NAME.  Your training logs
will be saved along with a snapshot of your latest commit.

<aside class="notice">
You can always rerun *wandb init* to change your project's settings.
</aside>

## Automated Environments

```shell
# This is secret and shouldn't be checked into version control
WANDB_API_KEY=$YOUR_API_KEY
# Description is optional
WANDB_DESCRIPTION="$SHORT_MESSAGE"
```

```shell
# Only needed if you don't checkin the wandb/settings file
WANDB_ENTITY=$username
WANDB_PROJECT=$project
```

```python
# Only needed if you don't checkin the wandb/settings file
os.makedirs('wandb', exist_ok=True)
```

```python
# If you don't want to call your script with the wandb run wrapper
os.environ['WANDB_MODE'] = 'run'
```

When you are running your script in an automated environment, you can set environment variables before the script runs or within the script.  **WANDB_API_KEY** must be set if `wandb login` hasn't been run on the remote machine, you can find it [here](https://app.wandb.ai/profile).  Optionally you can set **WANDB_DESCRIPTION** to something that will become the name of your runs in the UI.

If you have run `wandb init` in the directory of your training script a directory named *wandb* will be created and will contain a settings file with your entity and project set in it.  If you check this file in, you do not need to set **WANDB_ENTITY** and **WANDB_PROJECT**.  If you want to set these dynamically or don't wish to checkin this file, you can use the environment variables.

If you chose not to checkin the `wandb/settings` file with your project, a directory named `wandb` must exist in the same directory that the script is run in.

If you don't want to run your script with the `wandb run` wrapper, you must set the **WANDB_MODE** environment variable to "run" to have your runs synced to the cloud.  Otherwise, just be sure your automated process calls your script like: `wandb run python train.py --args`