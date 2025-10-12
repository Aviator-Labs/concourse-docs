---
title: Golang library testing example
hide:
  - toc
---

You can run the tests for a Golang library across any specified versions.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/golang-lib" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

This example shows how to have multiple versions of a language, environment, or dependency fetched and integrated in to
a [Pipeline](https://concourse-ci.org/pipelines.html).

For these Docker images, defining them as [Resources](https://concourse-ci.org/resources.html) has two advantages for
this use case. First, this enables the pipeline to be triggered when there are new versions of those images available.
Second, referencing them in the task's [`task` step **`image`
**](https://concourse-ci.org/task-step.html#schema.task.image) param is helpful as it will ensure consistency between
the image versions fetched by the [Resource](https://concourse-ci.org/resources.html) and the image version running in
the job.

## Pipeline Configuration

```yaml
---
resources:
  - name: golang-1.20.x-image
    type: registry-image
    icon: docker
    source:
      repository: golang
      tag: 1.20-alpine

  - name: golang-1.21.x-image
    type: registry-image
    icon: docker
    source:
      repository: golang
      tag: 1.21-alpine

  - name: golang-1.22.x-image
    type: registry-image
    icon: docker
    source:
      repository: golang
      tag: 1.22-alpine

task-config: &task-config
  platform: linux
  run:
    path: /bin/sh
    args:
      - -c
      - |
        GOPATH=$PWD/go

        go version

jobs:
  - name: golang-1.20
    public: true
    build_log_retention:
      builds: 50
    plan:
      - get: golang-1.20.x-image
        trigger: true
      - task: run-tests
        image: golang-1.20.x-image
        config:
          <<: *task-config

  - name: golang-1.21
    public: true
    build_log_retention:
      builds: 50
    plan:
      - get: golang-1.21.x-image
        trigger: true
      - task: run-tests
        image: golang-1.21.x-image
        config:
          <<: *task-config

  - name: golang-1.22
    public: true
    build_log_retention:
      builds: 50
    plan:
      - get: golang-1.22.x-image
        trigger: true
      - task: run-tests
        image: golang-1.22.x-image
        config:
          <<: *task-config
```

## References

* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)