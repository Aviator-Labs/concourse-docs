---
title: Nodejs application testing example
---

You can run the tests for a Nodejs application.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/nodejs" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

## Pipeline Configuration

```yaml
---
resources:
  - name: repo
    type: git
    icon: github
    source:
      uri: https://github.com/nodejs/nodejs.org.git

  - name: node-image
    type: registry-image
    icon: docker
    source:
      repository: node
      tag: 22-slim

jobs:
  - name: test
    public: true
    build_log_retention:
      builds: 50
    plan:
      - get: node-image
      - get: repo
        trigger: true
      - task: test
        image: node-image
        config:
          inputs:
            - name: repo
          platform: linux
          run:
            path: bash
            dir: repo
            args:
              - -cex
              - |
                npm install -g pnpm
                pnpm install --frozen-lockfile
                pnpm run test
```

## References

* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)