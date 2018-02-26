# WandB Board

> Starts the local server from the current directory

```shell
wandb board
```

> Starts the server on top of a custom directory

```shell
wandb board --logdir=~/other/directory
```

WandB board is an open source and local version of our metric tracking solution.  It scans a 
local directory for existing and new runs and starts a webserver that provides the interface and backend.

## Contributing

If you would like to contribute to the board, checkout the [repository](https://github.com/wandb/client/blob/master/DEVELOPMENT.md)