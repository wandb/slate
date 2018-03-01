# Tracking Models
## Configurations

![History](configuration.png)

```python--tensorflow
run = wandb.init()
run.config.epochs = 4   # config variables are saved to the cloud

flags = tf.app.flags
flags.DEFINE_string('data_dir', '/tmp/data')
flags.DEFINE_integer('batch_size', 128, 'Batch size.')
run.config.update(flags.FLAGS)  # adds all of the tensorflow flags as config variables
```

```python--keras
run = wandb.init()
run.config.epochs = 4   # config variables are saved to the cloud

parser = argparse.ArgumentParser()
parser.add_argument('--batch-size', type=int, default=8, metavar='N',
                     help='input batch size for training (default: 8)')
run.config.update(args) # adds all of the arguments as config variables
```

```python--pytorch
run = wandb.init()
run.config.epochs = 4   # config variables are saved to the cloud

parser = argparse.ArgumentParser()
parser.add_argument('--batch-size', type=int, default=8, metavar='N',
                     help='input batch size for training (default: 8)')
run.config.update(args) # adds all of the arguments as config variables
```

Configurations is a way of automatically tracking the hyperparameters you used
to build your model.

You can set the configuration values directly and access them as ordinary variables, or
you can import variables from tensorflow flags or argparse objects to integrate
with pre-existing code.

### File-Based Configs

> (Optional) Create a config-defaults file to automatically load hyperparameters
> into the config variable.

```yaml
# sample config-defaults file
epochs:
  desc: Number of epochs to train over
  value: 100
batch_size:
  desc: Size of each mini-batch
  value: 32
```

You can create a file called config-defaults.yaml and it will automatically
be loaded into the config variable.

You can tell wandb to load different config files with the argument `--configs special-configs.yaml` which will load parameters from the file special-configs.yaml.

> Automatically load the yaml file into the config object

```shell
wandb run train.py
```

> Change the config file used to load the config object

```shell
wandb run --configs special-configs.yaml
```

> Mutiple config files are allowed

```shell
wandb run --configs special-configs.yaml,extra-configs.yaml
```

## History
![History](history.png)

```python--tensorflow
run = wandb.init(config=flags.FLAGS)

# Start training
with tf.Session() as sess:
  sess.run(init)

  for step in range(1, run.config.num_steps+1):
      batch_x, batch_y = mnist.train.next_batch(run.config.batch_size)
      # Run optimization op (backprop)
      sess.run(train_op, feed_dict={X: batch_x, Y: batch_y})
      # Calculate batch loss and accuracy
      loss, acc = sess.run([loss_op, accuracy], feed_dict={X: batch_x, Y: batch_y})

      run.history.add({'acc': acc, 'loss':loss})   # log accuracy and loss
```

```python--keras
run = wandb.init(config=args)

def keras_log(epoch, logs):
  run.history.add({'loss': logs['loss'], 'acc': logs['acc']})
  run.summary['acc'] = logs['acc']

model.fit(train, labels, callbacks=[LambdaCallback(keras_log)])
```

```python--pytorch
run = wandb.init(config=args)

for epoch in range(1, args.epochs + 1):
  train_loss = train(epoch)
  test_loss, test_accuracy = test()

  torch.save(model.state_dict(), 'model')

  run.history.add({"loss": train_loss, "val_loss": test_loss})
```

The history object is used to track metrics that change as the model trains.  You can access 
a mutable dictionary of metrics via `run.history.row`.  The row will be saved and a new row created when 
`run.history.add` is called.  For simplicity, you can call run.history.add and pass in a dictionary of all the metrics you would like to save.

### Context Manager

We provide a context manager that automatically calls `add`
and accepts an optional boolean to help keep nested code clean.

> Context manager

```python
with run.history.step(batch_idx % log_interval == 0):
  run.history.row.update({"metric": 1})
  if run.history.compute:
    # Something expensive here
```

<!---
## Media

```python
run.history.row["examples"] = [wandb.Image(numpy_array_or_pil, caption="Label")]
run.history.add()
```

The history object also accepts rich media.  Currently only images are supported.  Media is added
by supplying a list of wandb media objects.


If a numpy array is supplied we assume it's gray scale if the last dimension is 1, RGB if it's 3, 
and RGBA if it's 4.  If the array contains floats we convert them to ints between 0 and 255.  
You can specify a [mode](https://pillow.readthedocs.io/en/3.1.x/handbook/concepts.html#concept-modes) 
manually or just supply a `PIL.Image`.  We recommend you don't add more than 20-50 images per step.
-->

## Summary

![Summary](summary.png)

```python
run = wandb.init(config=args)

for epoch in range(1, args.epochs + 1):
  test_loss, test_accuracy = test()
  run.summary["accuracy"] = test_accuracy
```

The summary statistics are used to track single metrics per model.  If a summary
metric is modified, only the updated state is saved.  We automatically set summary to the last
history row added unless you modify it manually.

## Keras Callback

> Simpler way to track metrics in keras using wandb.callbacks.Keras

```python
import wandb
run = wandb.init()

model.fit(X_train, y_train,  validation_data=(X_test, y_test),
          callbacks=[wandb.callbacks.Keras()])
```

If you are using keras, you can use the Keras callback to automatically save
all the metrics and the loss values tracked in `model.fit`.

## Saving Models

![Saved](saved.png)

```python--keras
import wandb
run = wandb.init()

model.fit(X_train, y_train,  validation_data=(X_test, y_test),
    callbacks=[wandb.callbacks.Keras()])
model.save(os.path.join(run.dir, "model.h5")) #
```

Wandb will save to the cloud any files put in wandb's run directory.

Wandb's run directories are inside the wandb directory and the path looks like _run-20171023_105053-3o4933r0_ where _20171023_105053_ is the timestamp and _3o4933r0_ is the ID of the run.

## Restoring Code State

```
# creates a branch and restores the code to the state it was in when run $RUN_ID was executed
wandb restore $RUN_ID
```

When `wandb.init` is called from your script, a link is saved to the last git commit if the code
is in a git repository.  A diff patch is also created in case there are uncommitted changes or changes
that are out of sync with your remote.


