---
title: User Roles & Permissions
---

Concourse comes with five roles:

1. Concourse Admin
2. Team Owner
3. Team Member
4. Pipeline Operator
5. Team Viewer

These roles are strictly ordered, so that each role always has all the permissions of any other role lower on the list.
This means that a Pipeline Operator can always do anything a Team Viewer can, and so on.

In this document we say an action is assigned to a role if that role is capable of performing the action, but any
less-privileged role is not. For example, the `SaveConfig` action is _assigned_ to the `member` role, so owners and
members can set a pipeline config, but pipeline operators and viewers cannot.

## Concourse Admin

`Admin` is a special user attribute granted only to [owners](#owner-role) of the [`main` team](main-team.md).

Admins have the ability to administrate teams using [`fly set-team`](managing-teams.md#fly-set-team), [
`fly destroy-team`](managing-teams.md#fly-destroy-team), [`fly rename-team`](managing-teams.md#fly-rename-team), etc.

Admins always have permission to perform any action on any team. You cannot assign actions to the admin role using the
`--config-rbac` flag.

The following actions are also assigned to admins, and cannot be reconfigured:

```yaml
- GetLogLevel
- ListActiveUsersSince
- SetLogLevel
- GetInfoCreds
- SetWall
- ClearWall
```

## `owner` role

Team owners have read, write and auth management capabilities within the scope of their team, and can rename or destroy
the team.

Actions assigned to the `owner` role by default:

```yaml
owner:
  - SetTeam
  - RenameTeam
  - DestroyTeam
```

## `member` role

Team members can operate within their team in a read & write fashion, but they can not change the configuration of their
team.

Actions assigned to the `member` role by default:

```yaml
member:
  - SaveConfig
  - CreateBuild
  - DeletePipeline
  - OrderPipelines
  - OrderPipelinesWithinGroup
  - ExposePipeline
  - HidePipeline
  - RenamePipeline
  - ArchivePipeline
  - CreatePipelineBuild
  - RegisterWorker
  - LandWorker
  - RetireWorker
  - PruneWorker
  - HeartbeatWorker
  - DeleteWorker
  - HijackContainer
  - ReportWorkerContainers
  - ReportWorkerVolumes
  - CreateArtifact
  - GetArtifact
```

## `pipeline-operator` role

Team pipeline operators can perform pipeline operations such as triggering builds and pinning resources, however they
cannot update pipeline configurations.

Actions assigned to the `pipeline-operator` role by default:

```yaml
pipeline-operator:
  - AbortBuild
  - RerunJobBuild
  - CreateJobBuild
  - PauseJob
  - UnpauseJob
  - ClearTaskCache
  - UnpinResource
  - SetPinCommentOnResource
  - CheckResource
  - CheckResourceWebHook
  - CheckResourceType
  - EnableResourceVersion
  - DisableResourceVersion
  - PinResourceVersion
  - PausePipeline
  - UnpausePipeline
  - ClearResourceCache
```

## `viewer` role

Team viewers have "read-only" access to a team and its pipelines. This locks everything down, preventing users from
doing a [`fly set-pipeline`](https://concourse-ci.org/setting-pipelines.html#fly-set-pipeline) or [
`fly intercept`](https://concourse-ci.org/builds.html#fly-intercept).

Actions assigned to the `viewer` role by default:

```yaml
viewer:
  - GetConfig
  - GetCC
  - GetBuild
  - GetCheck
  - GetBuildPlan
  - ListBuilds
  - BuildEvents
  - BuildResources
  - GetBuildPreparation
  - GetJob
  - ListAllJobs
  - ListJobs
  - ListJobBuilds
  - ListJobInputs
  - GetJobBuild
  - GetVersionsDB
  - JobBadge
  - MainJobBadge
  - ListAllResources
  - ListResources
  - ListResourceTypes
  - GetResource
  - ListResourceVersions
  - GetResourceVersion
  - ListBuildsWithVersionAsInput
  - ListBuildsWithVersionAsOutput
  - GetResourceCausality
  - ListAllPipelines
  - ListPipelines
  - GetPipeline
  - ListPipelineBuilds
  - PipelineBadge
  - ListWorkers
  - DownloadCLI
  - GetInfo
  - ListContainers
  - GetContainer
  - ListDestroyingContainers
  - ListVolumes
  - ListDestroyingVolumes
  - ListTeams
  - GetTeam
  - ListTeamBuilds
  - ListBuildArtifacts
```

## Action Matrix

In this table, an action is marked as _customizable_ if it is possible to change its permissions by providing the
`--config-rbac` flag, documented below. Assigning an action to a role that is not customizable will have no effect on
its permissions.

| Action                        | `fly` commands affected                                                                                                                                                                                | UI actions affected                              | can be performed unauthenticated? |   customizable   |
|-------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------|:---------------------------------:|:----------------:|
| GetBuild                      | n/a                                                                                                                                                                                                    | view one-off build page                          |         :material-check:          | :material-check: |
| BuildResources                | n/a                                                                                                                                                                                                    | view build page                                  |         :material-check:          | :material-check: |
| GetBuildPreparation           | n/a                                                                                                                                                                                                    | view build page                                  |         :material-check:          | :material-check: |
| BuildEvents                   | [`fly watch`](https://concourse-ci.org/builds.html#fly-watch),[`fly execute`](https://concourse-ci.org/tasks.html#running-tasks)                                                                       | view build page                                  |         :material-check:          | :material-check: |
| GetBuildPlan                  | n/a                                                                                                                                                                                                    | view build page                                  |         :material-check:          | :material-check: |
| ListBuildArtifacts            | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-check: |
| AbortBuild                    | [`fly abort-build`](https://concourse-ci.org/builds.html#fly-abort-build)                                                                                                                              | abort button on build page                       |         :material-close:          | :material-check: |
| PruneWorker                   | [`fly prune-worker`](https://concourse-ci.org/administration.html#fly-prune-worker)                                                                                                                    | n/a                                              |         :material-close:          | :material-check: |
| LandWorker                    | [`fly land-worker`](https://concourse-ci.org/administration.html#fly-land-worker)                                                                                                                      | n/a                                              |         :material-close:          | :material-check: |
| RetireWorker                  | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| ListDestroyingVolumes         | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| ListDestroyingContainers      | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| ReportWorkerContainers        | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| ReportWorkerVolumes           | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| GetPipeline                   | n/a                                                                                                                                                                                                    | view pipeline page                               |         :material-check:          | :material-check: |
| GetJobBuild                   | n/a                                                                                                                                                                                                    | view build page                                  |         :material-check:          | :material-check: |
| PipelineBadge                 | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-check: |
| JobBadge                      | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-check: |
| ListJobs                      | [`fly jobs`](https://concourse-ci.org/jobs.html#fly-jobs)                                                                                                                                              | view pipeline page                               |         :material-check:          | :material-check: |
| GetJob                        | n/a                                                                                                                                                                                                    | view job page                                    |         :material-check:          | :material-check: |
| ListJobBuilds                 | [`fly builds`](https://concourse-ci.org/builds.html#fly-builds)                                                                                                                                        | view job page                                    |         :material-check:          | :material-check: |
| ListPipelineBuilds            | [`fly builds`](https://concourse-ci.org/builds.html#fly-builds)                                                                                                                                        | n/a                                              |         :material-check:          | :material-check: |
| GetResource                   | n/a                                                                                                                                                                                                    | view resource page                               |         :material-check:          | :material-check: |
| ListBuildsWithVersionAsInput  | n/a                                                                                                                                                                                                    | expand version on resource page                  |         :material-check:          | :material-check: |
| ListBuildsWithVersionAsOutput | n/a                                                                                                                                                                                                    | expand version on resource page                  |         :material-check:          | :material-check: |
| GetResourceCausality          | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-check: |
| GetResourceVersion            | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-check: |
| ListResources                 | fly resources                                                                                                                                                                                          | view pipeline page                               |         :material-check:          | :material-check: |
| ListResourceTypes             | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-check: |
| ListResourceVersions          | fly resource-versions,[`fly pin-resource`](https://concourse-ci.org/managing-resources.html#fly-pin-resource)                                                                                          | view resource page                               |         :material-check:          | :material-check: |
| CreateBuild                   | [`fly execute`](https://concourse-ci.org/tasks.html#running-tasks)                                                                                                                                     | n/a                                              |         :material-close:          | :material-check: |
| GetContainer                  | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-check: |
| HijackContainer               | [`fly intercept`](https://concourse-ci.org/builds.html#fly-intercept)                                                                                                                                  | n/a                                              |         :material-close:          | :material-check: |
| ListContainers                | [`fly containers`](https://concourse-ci.org/administration.html#fly-containers)                                                                                                                        | n/a                                              |         :material-close:          | :material-check: |
| ListWorkers                   | [`fly workers`](https://concourse-ci.org/administration.html#fly-workers)                                                                                                                              | n/a                                              |         :material-close:          | :material-check: |
| RegisterWorker                | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| HeartbeatWorker               | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| DeleteWorker                  | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| GetTeam                       | [`fly get-team`](https://concourse-ci.org/managing-teams.html#fly-get-team)                                                                                                                            | n/a                                              |         :material-close:          | :material-check: |
| SetTeam                       | [`fly set-team`](https://concourse-ci.org/managing-teams.html#fly-set-team)                                                                                                                            | n/a                                              |         :material-close:          | :material-check: |
| ListTeamBuilds                | [`fly builds`](https://concourse-ci.org/builds.html#fly-builds)                                                                                                                                        | n/a                                              |         :material-close:          | :material-check: |
| RenameTeam                    | [`fly rename-team`](https://concourse-ci.org/managing-teams.html#fly-rename-team)                                                                                                                      | n/a                                              |         :material-close:          | :material-check: |
| DestroyTeam                   | [`fly destroy-team`](https://concourse-ci.org/managing-teams.html#fly-destroy-team)                                                                                                                    | n/a                                              |         :material-close:          | :material-check: |
| ListVolumes                   | [`fly volumes`](https://concourse-ci.org/administration.html#fly-volumes)                                                                                                                              | n/a                                              |         :material-close:          | :material-check: |
| DownloadCLI                   | [`fly sync`](https://concourse-ci.org/fly.html#fly-sync)                                                                                                                                               | icons on dashboard and pipeline pages            |         :material-check:          | :material-close: |
| CheckResourceWebHook          | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-close: |
| GetInfo                       | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-close: |
| GetCheck                      | [`fly check-resource`](https://concourse-ci.org/managing-resources.html#fly-check-resource),[`fly check-resource-type`](https://concourse-ci.org/managing-resource-types.html#fly-check-resource-type) | check button on resource page                    |         :material-close:          | :material-check: |
| ListTeams                     | [`fly teams`](https://concourse-ci.org/managing-teams.html#fly-teams)                                                                                                                                  | view dashboard page                              |         :material-check:          | :material-close: |
| ListAllPipelines              | n/a                                                                                                                                                                                                    | view dashboard page                              |         :material-check:          | :material-close: |
| ListPipelines                 | [`fly pipelines`](https://concourse-ci.org/managing-pipelines.html#fly-pipelines)                                                                                                                      | n/a                                              |         :material-check:          | :material-check: |
| ListAllJobs                   | [`fly teams`](https://concourse-ci.org/managing-teams.html#fly-teams)                                                                                                                                  | view dashboard page                              |         :material-check:          | :material-close: |
| ListAllResources              | n/a                                                                                                                                                                                                    | view dashboard page                              |         :material-check:          | :material-close: |
| ListBuilds                    | [`fly builds`](https://concourse-ci.org/builds.html#fly-builds)                                                                                                                                        | n/a                                              |         :material-check:          | :material-close: |
| MainJobBadge                  | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-close: |
| GetLogLevel                   | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| SetLogLevel                   | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| GetWall                       | n/a                                                                                                                                                                                                    | n/a                                              |         :material-check:          | :material-close: |
| SetWall                       | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| ClearWall                     | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| ListActiveUsersSince          | [`fly active-users`](https://concourse-ci.org/managing-teams.html#fly-active-users)                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| GetInfoCreds                  | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-close: |
| CheckResource                 | [`fly check-resource`](https://concourse-ci.org/managing-resources.html#fly-check-resource)                                                                                                            | check button on resource page                    |         :material-close:          | :material-check: |
| CheckResourceType             | [`fly check-resource-type`](https://concourse-ci.org/managing-resource-types.html#fly-check-resource-type)                                                                                             | n/a                                              |         :material-close:          | :material-check: |
| CreateJobBuild                | [`fly trigger-job`](https://concourse-ci.org/jobs.html#fly-trigger-job)                                                                                                                                | trigger button on job and build pages            |         :material-close:          | :material-check: |
| RerunJobBuild                 | [`fly rerun-build`](https://concourse-ci.org/jobs.html#fly-rerun-build)                                                                                                                                | rerun button on build page                       |         :material-close:          | :material-check: |
| CreatePipelineBuild           | [`fly execute`](https://concourse-ci.org/tasks.html#running-tasks)                                                                                                                                     | n/a                                              |         :material-close:          | :material-check: |
| DeletePipeline                | [`fly destroy-pipeline`](https://concourse-ci.org/managing-pipelines.html#fly-destroy-pipeline)                                                                                                        | n/a                                              |         :material-close:          | :material-check: |
| DisableResourceVersion        | [`fly disable-resource-version`](https://concourse-ci.org/managing-resources.html#fly-disable-resource-version)                                                                                        | version disable widget on resource page          |         :material-close:          | :material-check: |
| EnableResourceVersion         | [`fly enable-resource-version`](https://concourse-ci.org/managing-resources.html#fly-enable-resource-version)                                                                                          | version enable widget on resource page           |         :material-close:          | :material-check: |
| PinResourceVersion            | [`fly pin-resource`](https://concourse-ci.org/managing-resources.html#fly-pin-resource)                                                                                                                | pin buttons on resource page                     |         :material-close:          | :material-check: |
| UnpinResource                 | fly unpin-resource                                                                                                                                                                                     | pin buttons on resource page                     |         :material-close:          | :material-check: |
| SetPinCommentOnResource       | [`fly pin-resource`](https://concourse-ci.org/managing-resources.html#fly-pin-resource)                                                                                                                | comment overlay on resource page                 |         :material-close:          | :material-check: |
| GetConfig                     | [`fly get-pipeline`](https://concourse-ci.org/managing-pipelines.html#fly-get-pipeline)                                                                                                                | n/a                                              |         :material-close:          | :material-check: |
| GetCC                         | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-check: |
| GetVersionsDB                 | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-check: |
| ListJobInputs                 | n/a                                                                                                                                                                                                    | n/a                                              |         :material-close:          | :material-check: |
| OrderPipelines                | [`fly order-pipelines`](https://concourse-ci.org/managing-pipelines.html#fly-order-pipelines)                                                                                                          | drag and drop on dashboard                       |         :material-close:          | :material-check: |
| OrderPipelinesWithinGroup     | [`fly order-instanced-pipelines`](https://concourse-ci.org/instanced-pipelines.html#fly-order-instanced-pipelines)                                                                                     | drag and drop within instance group on dashboard |         :material-close:          | :material-check: |
| PauseJob                      | [`fly pause-job`](https://concourse-ci.org/jobs.html#fly-pause-job)                                                                                                                                    | pause button on job page                         |         :material-close:          | :material-check: |
| PausePipeline                 | [`fly pause-pipeline`](https://concourse-ci.org/managing-pipelines.html#fly-pause-pipeline)                                                                                                            | pause button on pipeline or dashboard            |         :material-close:          | :material-check: |
| RenamePipeline                | [`fly rename-pipeline`](https://concourse-ci.org/managing-pipelines.html#fly-rename-pipeline)                                                                                                          | n/a                                              |         :material-close:          | :material-check: |
| UnpauseJob                    | [`fly unpause-job`](https://concourse-ci.org/jobs.html#fly-unpause-job)                                                                                                                                | play button on job page                          |         :material-close:          | :material-check: |
| UnpausePipeline               | [`fly unpause-pipeline`](https://concourse-ci.org/managing-pipelines.html#fly-unpause-pipeline)                                                                                                        | play button on pipeline or dashboard             |         :material-close:          | :material-check: |
| ExposePipeline                | [`fly expose-pipeline`](https://concourse-ci.org/managing-pipelines.html#fly-expose-pipeline)                                                                                                          | eyeball button on dashboard                      |         :material-close:          | :material-check: |
| HidePipeline                  | [`fly hide-pipeline`](https://concourse-ci.org/managing-pipelines.html#fly-hide-pipeline)                                                                                                              | slashed eyeball button on dashboard              |         :material-close:          | :material-check: |
| SaveConfig                    | [`fly set-pipeline`](https://concourse-ci.org/setting-pipelines.html#fly-set-pipeline)                                                                                                                 | n/a                                              |         :material-close:          | :material-check: |
| ClearTaskCache                | [`fly clear-task-cache`](https://concourse-ci.org/jobs.html#fly-clear-task-cache)                                                                                                                      | n/a                                              |         :material-close:          | :material-check: |
| CreateArtifact                | [`fly execute`](https://concourse-ci.org/tasks.html#running-tasks)                                                                                                                                     | n/a                                              |         :material-close:          | :material-check: |
| GetArtifact                   | [`fly execute`](https://concourse-ci.org/tasks.html#running-tasks)                                                                                                                                     | n/a                                              |         :material-close:          | :material-check: |
| ClearResourceCache            | [`fly clear-resource-cache`](https://concourse-ci.org/managing-resources.html#fly-clear-resource-cache)                                                                                                | n/a                                              |         :material-close:          | :material-check: |

## Configuring RBAC

!!! warning "Experimental Feature"

    Configuring RBAC is **experimental**, and this may change in the future.

It is possible to promote or demote the roles to which actions are assigned by passing the `--config-rbac` to the
`concourse web` command with a path to a `.yml` file, like the following:

```shell
concourse web --config-rbac=/path/to/rbac/config.yml
```

This file should be a YAML map where the keys are role names (`owner`, `member`, `pipeline-operator`, and `viewer` are
valid). For each role, the value should be a list of actions. On startup, Concourse will assign each role to its
associated list of actions.

For example, in the default configuration only pipeline-operators and above can abort builds. To restrict aborting
builds to only members and above, you could pass this as a `--config-rbac` file:

```yaml
member:
  - AbortBuild
```

On the other hand, only members and above can order pipelines by default. To extend this privilege down to
pipeline-operators, you can use a `--config-rbac` file like the following:

```yaml
pipeline-operator:
  - OrderPipelines
```

You do not need to specify a role for every possible action; if an action does not appear in the file, then the default
role (as described in the sections above) will be assigned to that action. Also, please avoid specifying the same action
under multiple roles in this file - it can have unpredictable results.