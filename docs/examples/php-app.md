---
title: PHP application testing example
hide:
  - toc
---

You can run the tests for a PHP application.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/php" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

## Pipeline Configuration

```yaml
---
resources:
  - name: laravel-git
    type: git
    icon: github
    source:
      uri: https://github.com/laravel/laravel.git

jobs:
  - name: test
    public: true
    build_log_retention:
      builds: 50
    plan:
      - get: laravel-git
        trigger: true
      - task: run-tests
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: composer }
          inputs:
            - name: laravel-git
          run:
            path: /bin/sh
            args:
              - -ce
              - |
                cd laravel-git

                composer install

                cp .env.example .env
                php artisan key:generate

                vendor/bin/phpunit
```

## References

* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)