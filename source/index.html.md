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
  - board
  - sweep


search: true
---

# Introduction
```shell
# Install wandb
pip install wandb
```

WandB is a tool for people building machine learning models.  

WandB helps with:

1.  Tracking, saving and reproducing models.  
2.  Visualizing results across models.
3.  Automating large-scale hyperparameter search.

# Getting Started

## Running Locally

> Near the top of your training script add our initialization code:

```python
# Inside my model training code
import wandb
run = wandb.init()

config = run.config 
config.dropout = 0.2
config.hidden_layer_size = 128

# Run my training code
my_train_loop()
```

```shell
# Run your script normally from the commandline
python learn.py
```

WandB can be run locally or used as a cloud service.

Running WandB locally:

1. Add the wandb.init() line near the top of your training script
2. From the commandline run `wandb board` from the same directory you ran your training script. 

## Syncing runs to the cloud

```shell
# Initialize wandb in the root directory of your project
wandb init
```

```shell
# This runs your script and syncs all metrics and metadata to the cloud
wandb run learn.py
```

Running WandB with the wandb.ai cloud service:

1. Sign up for an account by going to our [sign up page](https://app.wandb.ai/login?invited).
2. Run `wandb init` from the command line.  You will be prompted for a team name and a project name.  This will create a
wandb directory that contains a settings file with the information you provided.  You can optionally check the **wandb/settings** file 
into version control.  All other files and folders in the wandb directory are automatically ignored.

3. Run your training script as `wandb run learn.py`.  Now, a new record will
be added to _https://app.wandb.ai/**$ENTITY_NAME**/**$PROJECT_NAME**_.  Your training logs will be saved along with a snapshot of your latest commit.

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