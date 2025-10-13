---
title: Running a worker node
---

# Running a `worker` node

The `worker` node registers with the [`web` node](running-web.md) and is then used for executing builds and performing
resource `check`s. It doesn't really decide much on its own.

## Prerequisites

* Linux:
    * We test and support the following distributions. Minimum kernel version tested is 5.15.
        * Ubuntu 22.04
        * Ubuntu 24.04
    * Other Requirements:
        * User namespaces must be enabled.
        * To enforce memory limits on tasks, memory + swap accounting must be enabled.
* Windows/Darwin:
    * no special requirements (that we know of).

!!! note

    Windows containers are currently not supported and Darwin does not have native containers. Steps will run inside 
    a temporary directory on the Windows/Darwin worker. Any dependencies needed for your tasks (e.g. git, .NET, golang, 
    ssh) should be pre-installed on the worker. Windows/Darwin workers do not come with any resource types.

## Running `concourse worker`

The `concourse` CLI can run as a `worker` node via the `worker` subcommand.

First, you'll need to configure a directory for the worker to store data:

```shell
CONCOURSE_WORK_DIR=/opt/concourse/worker
```

This is where all the builds run, and where all resources are fetched in to, so make sure it's backed by enough storage.

Next, point the worker at your [`web` node](running-web.md) like so:

```shell
CONCOURSE_TSA_HOST=10.0.2.15:2222
CONCOURSE_TSA_PUBLIC_KEY=path/to/tsa_host_key.pub
CONCOURSE_TSA_WORKER_PRIVATE_KEY=path/to/worker_key
```

Finally start the worker:

```shell
# run with -E to forward env config, or just set it all as root
sudo -E concourse worker
```

Note that the worker must be run as `root` because it orchestrates containers.

All logs will be emitted to `stdout`, with any panics or lower-level errors being emitted to `stderr`.

### Resource utilization

**CPU usage**: almost entirely subject to pipeline workloads. More resources configured will result in more checking,
and in-flight builds will use as much CPU as they want.

**Memory usage**: also subject to pipeline workloads. Expect usage to increase with the number of containers on the
worker and spike as builds run.

**Bandwidth usage**: again, almost entirely subject to pipeline workloads. Expect spikes from periodic checking, though
the intervals should spread out over enough time. Resource fetching and pushing will also use arbitrary bandwidth.

**Disk usage**: arbitrary data will be written as builds run, and resource caches will be kept and garbage collected on
their own life cycle. We suggest going for a larger disk size if it's not too much trouble. All state on disk must not
outlive the worker itself; it is all ephemeral. If the worker is re-created (i.e. fresh VM/container and all processes
were killed), it should be brought back with an empty disk.

**Highly available**: not applicable. Workers are inherently singletons, as they're being used as drivers running
entirely different workloads.

**Horizontally scalable**: yes; workers directly correlate to your capacity required by however many pipelines,
resources, and in-flight builds you want to run. It makes sense to scale them up and down with demand.

**Outbound traffic**:

* External traffic to arbitrary locations as a result of periodic resource checking and running builds
* External traffic to the `web` node's configured external URL when downloading the inputs for a [
  `fly execute`](https://concourse-ci.org/tasks.html#running-tasks)
* External traffic to the `web` node's TSA port (`2222`) for registering the worker
* If P2P streaming is enabled there will be traffic to other workers.

**Inbound traffic**:

* From the [`web` node](running-web.md) on port `7777` (Garden) and `7788` (BaggageClaim). These ports do not need to be
  exposed, they are forwarded to the web node via the ssh connection on port `2222`.
* If P2P streaming is enabled there will be traffic to other workers.

## Operating a `worker` node

### Scaling Workers

#### Horizontal vs Vertical Scaling

### Worker Heartbeating & Stalling

### Restarting a Worker

### Gracefully Removing a Worker

## Configuring the `worker` node

### Tagging Workers

### Team Workers

#### Tags and Team Workers

### Healthcheck Endpoint

### Resource Types

#### Bundled Resource Types

#### Installing or Upgrading Bundled Resource Types

### Configuring Runtimes

#### `containerd` runtime

#### Transitioning from Guardian to containerd

#### `Guardian` runtime

#### Troubleshooting and fixing DNS resolution

##### Pointing to external DNS servers

##### Using a local DNS server

##### A note on allowing host access and DNS proxy

### Configuring Peer-to-Peer Volume Streaming

#### P2P Worker Configuration

#### P2P Web Configuration