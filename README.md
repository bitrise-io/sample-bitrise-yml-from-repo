# sample-bitrise-yml-from-repo

Sample project, demonstrates how you can use your `bitrise.yml` directly from your repository for builds on bitrise.io


## Guide

1. Create a new app on [bitrise.io](https://www.bitrise.io).
2. Replace it's `bitrise.yml` configuration with the one below.
3. Start a new build.


## "Host" `bitrise.yml`, drop-in for bitrise.io

This configuration can be copy-pasted to [bitrise.io](https://www.bitrise.io),
and will properly map the inputs to run `bitrise` with the `bitrise.yml`
found in this (or your) repository.

**Note**: for public repositories / if you use a public `git clone` URL
you should remove the `activate-ssh-key` step!

```
---
format_version: 1.0.0
default_step_lib_source: https://github.com/bitrise-io/bitrise-steplib.git
trigger_map:
- pattern: "*"
  is_pull_request_allowed: true
  workflow: primary
workflows:
  primary:
    steps:
    - activate-ssh-key@3.0.2: {}
    - git-clone@3.0.0: {}
    - script@0.9.1:
        inputs:
        - content: |-
            #!/bin/bash
            bitrise trigger "$BITRISE_GIT_BRANCH"
```

## Important Security notes

Be aware that if you use this method **anyone can change your `bitrise.yml` file**
via Pull Requests!

You should only allow this if you're absolutely sure that no private/secret
information is exposed for builds started by Pull Requests!
