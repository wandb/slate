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

WandB is a tool that makes building machine learning models more efficient and more fun.

WandB helps with:

1.  Tracking, saving and reproducing models.  
2.  Visualizing results across models.
3.  Automating large-scale hyperparameter search.

![Run Page](run-page.png)
![Runs Page](runs-tracking.png)

# Getting Started

WandB is easy to install.  Once you have download the python library, you just need to add a few lines of code to your training script.

<!---
WandB can be run locally or used as a cloud service.  Either way integrating wandb is pretty simple.  You can see some example learning code with integrations using Pytorch, Keras and TensorFlow at <https://github.com/wandb/examples>.

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

Running WandB locally:

1. Add the line `run = wandb.init()` line near the top of your training script
2. From the commandline run `wandb board` from the same directory you ran your training script. 
3. Save configuration parameters in `run.config`, save output to `run.history` and save models to `run.dir`
-->


## Syncing Runs to the Cloud

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

When you are running your script in an automated environment, you can control wandb with environment variables set before the script runs or within the script.

Relevant environment variables:

Variable name | Usage
------------- | -----
**WANDB_API_KEY** | Sets the authentication key associated with your account.  You can find your key at <https://app.wandb.ai/profile>.  This must be set if `wandb login` hasn't been run on the remote machine.
**WANDB_DESCRIPTION** | Description associated with a run.  This will become the name of your run in the UI.  If not set it will be randomly generated for you
**WANDB_ENTITY** | The entity associated with your run.  If you have run `wandb init` in the directory of your training script, it will create a directory named *wandb* and will save a default entity which can be checked into source control.  If you don't want to create that file or want to override the file you can use the environmental variable.
**WANDB_PROJECT** | The project associated with your run.  This can also be set with `wandb init`, but the environmental variable will override the value.
**WANDB_MODE** | Set this to *run* if you want to save your run to the cloud.  Another way to do this is to run your script `train.py` with the command `wandb run train.py`.
