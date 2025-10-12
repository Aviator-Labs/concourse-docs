---
title: Task inputs and outputs example
hide:
  - toc
---

A task can pass an artifacts to another task in the same job.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/task-passing-artifact/jobs/create-and-consume/builds/1" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

Tasks within a job have the ability to pass artifacts directly inbetween them to allow you to process artifacts in many
ways.

While you are free to create as many jobs as you'd like for your pipeline, you have to use resources to pass artifacts
inbetween them.

These constructs give you the ability to design a pipeline that can process artifacts in many different ways
via [Tasks](https://concourse-ci.org/tasks.html), and then store those processed artifacts externally
via [Resources](https://concourse-ci.org/resources.html).

## Pipeline Configuration

```yaml
---
jobs:
  - name: create-and-consume
    public: true
    plan:
      - task: make-a-file
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: busybox }
          run:
            path: sh
            args:
              - -exc
              - ls -la; echo "Created a file on $(date)" > ./files/created_file
          outputs:
            - name: files
      - task: consume-the-file
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: busybox }
          inputs:
            - name: files
          run:
            path: cat
            args:
              - ./files/created_file
```

## References

* [`task-config.outputs`](https://concourse-ci.org/tasks.html#schema.task-config.outputs)
* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)