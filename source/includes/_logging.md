# Tracking Models
## Configurations

```python--tensorflow
run = wandb.init()
config = run.config # get the config object
config.epochs = 4   # config variables are saved to the cloud
                    # with a run every time they're set

flags = tf.app.flags
flags.DEFINE_string('data_dir', '/tmp/data')
flags.DEFINE_integer('batch_size', 128, 'Batch size.')
config.update(flags.FLAGS)  # adds all of the tensorflow flags as config variables
```

```python--keras
run = wandb.init()
config = run.config # get the config object
config.epochs = 4   # config variables are saved to the cloud
                    # with a run every time they're set

parser = argparse.ArgumentParser()
parser.add_argument('--batch-size', type=int, default=8, metavar='N',
                     help='input batch size for training (default: 8)')
config.update(args) # adds all of the arguments as config variables
```

```python--pytorch
run = wandb.init()
config = run.config # get the config object
config.epochs = 4   # config variables are saved to the cloud
                    # with a run every time they're set

parser = argparse.ArgumentParser()
parser.add_argument('--batch-size', type=int, default=8, metavar='N',
                     help='input batch size for training (default: 8)')
config.update(args) # adds all of the arguments as config variables
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

You can tell wandb to load different config files with the command

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

## Metrics



```python--tensorflow
run = wandb.init()
history = run.history
summary = run.summary

# Start training
with tf.Session() as sess:
  sess.run(init)

  for step in range(1, FLAGS.num_steps+1):
      batch_x, batch_y = mnist.train.next_batch(FLAGS.batch_size)
      # Run optimization op (backprop)
      sess.run(train_op, feed_dict={X: batch_x, Y: batch_y})
      # Calculate batch loss and accuracy
      loss, acc = sess.run([loss_op, accuracy], feed_dict={X: batch_x,
                                                               Y: batch_y})

      history.add({'acc': acc, 'loss':loss})   # log accuracy and loss
      summary['acc'] = acc  
      summary['loss'] = loss

```

```python--keras

run = wandb.init()
history = run.history
summary = run.summary

def log_discriminator(epoch, logs):
  history.add({
            'loss': logs['loss'],
            'acc': logs['acc']})
  summary['acc'] = logs['acc'])


wandb_logging_callback = LambdaCallback(on_epoch_end=log_generator)
model.fit(train, labels, callbacks=[wandb_logging_callback])
```

```python--pytorch
run = wandb.init()
history = run.history
summary = run.summary

for epoch in range(1, args.epochs + 1):
  train_loss = train(epoch)
  test_loss, test_accuracy = test()

  torch.save(model.state_dict(), 'model')

  history.add({"Train Loss": train_loss,
                   "Test Loss": test_loss})
  summary["Test Accuracy"] = test_accuracy

```
Metrics are a way of automatically tracking information about how the model
is performing.

### History

The history object is used to track metrics that change as the model runs.  Every
time the history object is updated the metrics are tracked.

### Summary

The summary statistics are used to track single metrics per model.  If a summary
metric is modified, only the updated state is saved.

### Keras Callback
> Simpler way to track metrics in keras using WandbKerasCallback

```python--keras
import wandb
from wandb.wandb_keras import WandbKerasCallback

run = wandb.init()
config = run.config

model.fit(X_train, y_train,  validation_data=(X_test, y_test),
    callbacks=[WandbKerasCallback()])
```

If you are using keras, you can use the WandbKerasCallback to automatically save
all the metrics and the loss values tracked in `model.fit`.

## Saving Models

```python--keras
import wandb
from wandb.wandb_keras import WandbKerasCallback

run = wandb.init()

model.fit(X_train, y_train,  validation_data=(X_test, y_test),
    callbacks=[WandbKerasCallback()])

model.save(os.path.join(run.dir, "model.h5")) #
```

Wandb will save to the cloud any files put in wandb's run directory.

Wandb's run directories are inside the wandb directory and the path looks like run-20171023_105053-3o4933r0 where 20171023_105053 is the timestamp and 3o4933r0
is the ID of the run.

## Saving Code State

When training scripts are run with `wandb run train.py` from the command line
a link is saved to the last git commit.  A diff patch is also created to the state
of the code at the time run in case there are uncommitted changes.

```
# restores the code to the state it was in when run $RUN_ID was executed
wandb restore $RUN_ID
```
