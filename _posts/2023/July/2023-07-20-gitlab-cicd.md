---
title:  "GitLab CI/CD"
layout: post
category: gitlab
author: jaxon
tags: ["gitlab", "cicd"]
---

# gitlab CICD
### Gitlab Runners
- Used for executing the pipelines in queue
- They're registered with the already running Gitlab instance
- Runners can have tags (So that in some cases, specific workloads are scheduled on a specific runner via these tags)

### Basic Blocks
#### Jobs
- Jobs are the smallest unit of a gitlab pipeline
- Defines the action that should be done
- Can specify which `runner` to be used via **tags**

#### Stages
- Can define dependencies between jobs

#### [Services](https://jira.telekom.de/servicedesk/customer/portal/141/CICD-55759)
- Commonly used to make a Database available in the CI (So that another container image can reach it later)
- The service can be accessed via the name defined under **alias**

```yaml
stages:
- test-mongo

test-mongo: # Job Name
  stage: test-mongo
  services:
    - alias: mongo
      name: mongo:7.0.0-rc8-jammy
  # We can reach mongo using alias name and the correct port number
  script: curl -v mongo:27017
  image: curlimages/curl:8.2.0
```
- I'm replicating locally using [**`gitlab-ci-local`**](https://github.com/firecow/gitlab-ci-local) and the following output was shortened

```bash
test-mongo $ curl -v mongo:27017
test-mongo > * Connected to mongo (172.21.0.2) port 27017
test-mongo > < HTTP/1.0 200 OK
test-mongo > It looks like you are trying to access MongoDB over HTTP on the native driver port.
PASS  test-mongo
```

---