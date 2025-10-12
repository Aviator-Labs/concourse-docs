---
title: Java application testing example
hide:
  - toc
---

You can run the tests for a Java application.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/java" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

## Pipeline Configuration

```yaml
---
resources:
  - name: apache-kafka
    type: git
    icon: github
    source:
      uri: https://github.com/apache/kafka.git

jobs:
  - name: test
    public: true
    build_log_retention:
      builds: 50
    plan:
      - get: apache-kafka
        trigger: true
      - task: run-tests
        config:
          platform: linux
          image_resource:
            type: registry-image
            source:
              repository: gradle
              tag: jdk17
          caches:
            - path: $HOME/.m2/repository
            - path: $HOME/.gradle/caches/
            - path: $HOME/.gradle/wrapper/
          inputs:
            - name: apache-kafka
          run:
            user: root
            path: /bin/sh
            args:
              - -ce
              - |
                cd apache-kafka

                ./gradlew clients:test --tests RequestResponseTest
```

## References

* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)