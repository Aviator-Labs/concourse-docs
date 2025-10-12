---
title: Job & task hooks example
---

Job hooks like [`job.on_success`](https://concourse-ci.org/jobs.html#schema.job.on_success) and Step hooks like [
`on_success`](https://concourse-ci.org/on-success-step.html#schema.on_success) are available to perform actions based on
the success, failure, or abortion of a job.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/hooks" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

## Pipeline Configuration

```yaml
---
task-config: &task-config
  platform: linux
  image_resource:
    type: registry-image
    source: { repository: busybox }

jobs:
  - name: job
    public: true
    plan:
      - task: successful-task
        config:
          <<: *task-config
          run:
            path: sh
            args: [ "-lc", "exit 0" ]
        on_success:
          task: task-success
          config:
            <<: *task-config
            run:
              path: echo
              args: [ "This task succeeded!" ]
        on_abort:
          task: task-aborted
          config:
            <<: *task-config
            run:
              path: echo
              args: [ "This task was aborted!" ]
      - task: failing-task
        config:
          <<: *task-config
          run:
            path: sh
            args: [ "-lc", "exit 1" ]
        on_failure:
          task: task-failure
          config:
            <<: *task-config
            run:
              path: echo
              args: [ "This task failed!" ]
    on_success:
      task: job-success
      config:
        <<: *task-config
        run:
          path: echo
          args: [ "This job succeeded!" ]
    on_failure:
      task: job-failure
      config:
        <<: *task-config
        run:
          path: echo
          args: [ "This job failed!" ]
    on_abort:
      task: job-aborted
      config:
        <<: *task-config
        run:
          path: echo
          args: [ "This job was aborted!" ]
```

## References

* [`job.on_success`](https://concourse-ci.org/jobs.html#schema.job.on_success)
* [`job.on_failure`](https://concourse-ci.org/jobs.html#schema.job.on_failure)
* [`job.on_abort`](https://concourse-ci.org/jobs.html#schema.job.on_abort)
* [`on_success`](https://concourse-ci.org/on-success-step.html#schema.on_success)
* [`on_failure`](https://concourse-ci.org/on-failure-hook.html#schema.on_failure)
* [`on_abort`](https://concourse-ci.org/on-abort-hook.html#schema.on_abort)
* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)