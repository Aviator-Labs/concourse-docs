---
title: Rails application testing example
hide:
  - toc
---

You can run the tests for a Rails that requires a specific version of ruby and relies on a Postgres database.

<div>
  <div style="position:relative;padding-top:40%;">
    <iframe src="https://ci.concourse-ci.org/teams/examples/pipelines/rails" allowfullscreen
      style="position:absolute;top:0;left:0;width:100%;height:100%;border:0"></iframe>
  </div>
</div>

## Pipeline Configuration

```yaml linenums="1"
---
resources:
  - name: rails-contributors-git
    type: git
    icon: github
    source:
      uri: https://github.com/rails/rails-contributors.git

jobs:
  - name: test
    public: true
    build_log_retention:
      builds: 50
    plan:
      - get: rails-contributors-git
        trigger: true
      - task: run-tests
        config:
          platform: linux
          image_resource:
            type: registry-image
            source: { repository: ruby, tag: 3.4.5 }
          inputs:
            - name: rails-contributors-git
          params:
            RAILS_ENV: test
            DATABASE_URL: postgresql://postgres@localhost
          run:
            path: /bin/bash
            args:
              - -ce
              - |
                cd rails-contributors-git

                echo "=== Setting up Postgres ==="
                apt-get update
                apt-get install -y postgresql libpq-dev cmake nodejs
                cat > /etc/postgresql/*/main/pg_hba.conf <<-EOF
                host   all   postgres   localhost   trust
                EOF
                service postgresql restart

                echo "=== Project requires that we clone rails ==="
                git clone --mirror https://github.com/rails/rails.git

                echo "=== Installing Gems ==="
                gem install -N bundler
                bundle install

                echo "=== Running Tests ==="
                bundle exec rails db:setup
                bundle exec rails test
```

## References

* [Jobs](https://concourse-ci.org/jobs.html)
* [Steps](https://concourse-ci.org/steps.html)
* [Tasks](https://concourse-ci.org/tasks.html)