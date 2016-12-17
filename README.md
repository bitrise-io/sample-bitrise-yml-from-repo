# sample-bitrise-yml-from-repo

Sample project, demonstrates how you can use your `bitrise.yml` directly from your repository for builds on bitrise.io


## Guide

1. Create a new app on [bitrise.io](https://www.bitrise.io).
2. Replace its `bitrise.yml` configuration with the one below.
3. Start a new build.


## "Host" `bitrise.yml`, drop-in for bitrise.io

This configuration can be copy-pasted to [bitrise.io](https://www.bitrise.io),
and will properly map the inputs to run `bitrise` with the `bitrise.yml`
found in this (or your) repository.

```
---
format_version: 1.4.0
default_step_lib_source: https://github.com/bitrise-io/bitrise-steplib.git

trigger_map:
- push_branch: "*"
  workflow: ci
- pull_request_target_branch: "*"
  workflow: ci

workflows:
  _run_from_repo:
    steps:
    - activate-ssh-key:
        run_if: '{{getenv "SSH_RSA_PRIVATE_KEY" | ne ""}}'
    - git-clone: {}
    - script:
        title: continue from repo
        inputs:
        - content: |-
            #!/bin/bash
            set -ex
            bitrise run "${BITRISE_TRIGGERED_WORKFLOW_ID}"
  ci:
    after_run:
    - _run_from_repo

  another-workflow:
    after_run:
    - _run_from_repo
```

## Important Security notes

Be aware that if you use this method **anyone can change your `bitrise.yml` file**
via Pull Requests!

You should only allow this if you're absolutely sure that no private/secret
information is exposed for builds started by Pull Requests!
