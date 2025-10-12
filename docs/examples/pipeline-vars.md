---
title: Pipeline ((vars)) example
hide:
  - toc
---

# Pipeline `((vars))` example

You can use params in a pipelines configuration file.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/pipeline-vars" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

## Pipeline Configuration

```yaml linenums="1"
---
jobs:
  - name: ((first))-job
    public: true
    plan:
      - task: simple-task
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: busybox }
          run:
            path: echo
            args: [ "Hello, ((hello))!" ]
  - name: level-((number))-job
    public: true
    plan:
      - task: simple-task
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: busybox }
          run:
            path: echo
            args: [ "Hello, ((hello))!" ]
```

## Variables

```yaml linenums="1"
---
first: initial
number: "9000"
hello: HAL
```

## References

* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)