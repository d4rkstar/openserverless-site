---
title: Download
description: Download OpenServerless with ops CLI
weight: 70
draft: false
---
## Download and Install `ops`

🚧 PAGE UNDER CONSTRUCTION  🚧

### Download links

OpenServerless can be installed with its Command Line Interface, `ops`.

For `ops` CLI We support 64-bit versions of recent Windows, macOS and major Linux distributions.
It is available for different architectures, and in different formats.

You can download the `ops` CLI from:

    https://github.com/apache/openserverless-cli/releases

then install it on your system.

### After the installation of the ops cli

Once installed, in the first run `ops` will tell to update the tasks
executing:

`ops -update`

This command updates the OpenServerless "tasks" (its internal logic) to the
latest version. This command should be also executed frequently, as the
tasks are continuously evolving and expanding.

`ops` will suggest when to update them (at least once a day).

You normally just need to update the tasks but sometimes you also need
to update `ops` itself. The system will detect when it is the case and
tell you what to do.
