---
title: Manually triggered job example
---

A job can be triggered by a resource. After it's complete, the next job can run automatically or manually.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/manual-trigger" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

## Pipeline Configuration

```yaml
---
resources:
  - name: every-30s
    type: time
    icon: clock-outline
    source:
      interval: 30s

jobs:
  - name: triggered-first
    public: true
    build_log_retention:
      builds: 20
    plan:
      - get: every-30s
        trigger: true
      - task: simple-task
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: busybox }
          run:
            path: echo
            args: [ "Hello, world!" ]
  - name: not-triggered
    public: true
    plan:
      - get: every-30s
        passed: [ triggered-first ]
      - task: simple-task
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: busybox }
          run:
            path: echo
            args: [ "Hello, world!" ]
  - name: triggered-second
    public: true
    build_log_retention:
      builds: 20
    plan:
      - get: every-30s
        passed: [ triggered-first ]
        trigger: true
      - task: simple-task
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: busybox }
          run:
            path: echo
            args: [ "Hello, world!" ]
```

## References

* [Resources](https://concourse-ci.org/resources.html)
* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)