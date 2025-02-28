---
title: "update"
description: "The update command description and usage"
keywords: "resources, update, dynamically"
---

## update

```markdown
Usage:  docker update [OPTIONS] CONTAINER [CONTAINER...]

Update configuration of one or more containers

Aliases:
  docker container update, docker update

Options:
      --blkio-weight uint16         Block IO (relative weight), between 10 and 1000, or 0 to disable (default 0)
      --cpu-period int              Limit CPU CFS (Completely Fair Scheduler) period
      --cpu-quota int               Limit CPU CFS (Completely Fair Scheduler) quota
      --cpu-rt-period int           Limit the CPU real-time period in microseconds
      --cpu-rt-runtime int          Limit the CPU real-time runtime in microseconds
  -c, --cpu-shares int              CPU shares (relative weight)
      --cpus decimal                Number of CPUs (default 0.000)
      --cpuset-cpus string          CPUs in which to allow execution (0-3, 0,1)
      --cpuset-mems string          MEMs in which to allow execution (0-3, 0,1)
      --help                        Print usage
      --kernel-memory string        Kernel memory limit
  -m, --memory string               Memory limit
      --memory-reservation string   Memory soft limit
      --memory-swap string          Swap limit equal to memory plus swap: '-1' to enable unlimited swap
      --pids-limit int              Tune container pids limit (set -1 for unlimited)
      --restart string              Restart policy to apply when a container exits
```

## Description

The `docker update` command dynamically updates container configuration.
You can use this command to prevent containers from consuming too many
resources from their Docker host.  With a single command, you can place
limits on a single container or on many. To specify more than one container,
provide space-separated list of container names or IDs.

With the exception of the `--kernel-memory` option, you can specify these
options on a running or a stopped container. On kernel version older than
4.6, you can only update `--kernel-memory` on a stopped container or on
a running container with kernel memory initialized.

> **Warning**
>
> The `docker update` and `docker container update` commands are not supported
> for Windows containers.
{: .warning }

## Examples

The following sections illustrate ways to use this command.

### <a name=cpu-shares></a> Update a container's cpu-shares (--cpu-shares)

To limit a container's cpu-shares to 512, first identify the container
name or ID. You can use `docker ps` to find these values. You can also
use the ID returned from the `docker run` command.  Then, do the following:

```console
$ docker update --cpu-shares 512 abebf7571666
```

### <a name=memory></a> Update a container with cpu-shares and memory (-m, --memory)

To update multiple resource configurations for multiple containers:

```console
$ docker update --cpu-shares 512 -m 300M abebf7571666 hopeful_morse
```

### <a name=kernel-memory></a> Update a container's kernel memory constraints (--kernel-memory)

You can update a container's kernel memory limit using the `--kernel-memory`
option. On kernel version older than 4.6, this option can be updated on a
running container only if the container was started with `--kernel-memory`.
If the container was started *without* `--kernel-memory` you need to stop
the container before updating kernel memory.

> **Note**
>
> The `--kernel-memory` option has been deprecated since Docker 20.10.

For example, if you started a container with this command:

```console
$ docker run -dit --name test --kernel-memory 50M ubuntu bash
```

You can update kernel memory while the container is running:

```console
$ docker update --kernel-memory 80M test
```

If you started a container *without* kernel memory initialized:

```console
$ docker run -dit --name test2 --memory 300M ubuntu bash
```

Update kernel memory of running container `test2` will fail. You need to stop
the container before updating the `--kernel-memory` setting. The next time you
start it, the container uses the new value.

Kernel version newer than (include) 4.6 does not have this limitation, you
can use `--kernel-memory` the same way as other options.

### <a name=restart></a> Update a container's restart policy (--restart)

You can change a container's restart policy on a running container. The new
restart policy takes effect instantly after you run `docker update` on a
container.

To update restart policy for one or more containers:

```console
$ docker update --restart=on-failure:3 abebf7571666 hopeful_morse
```

Note that if the container is started with "--rm" flag, you cannot update the restart
policy for it. The `AutoRemove` and `RestartPolicy` are mutually exclusive for the
container.
