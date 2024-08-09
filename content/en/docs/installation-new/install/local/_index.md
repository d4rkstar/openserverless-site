---
title: Local Machine
description: Install OpenServerless on a local machine
weight: 10
---
### Local Installation

This page describes how to install OpenServerless on your local machine. The
services are limited and not accessible from the outside, so this installation is useful only for development purposes.

### Prerequisites

Before installing, you need to:

- install [Docker](/docs/installation-new/prereq/docker/).

- install [ops](/docs/installation-new/download/).

- [configure](/docs/installation-new/configure/) the services you want

> 💡 **NOTE**
>
> ``https`` protocol and static publishing in a local
installation are not supported. If you enable them, the configuration will be ignored.

### Installation

Run the command:

    ops setup devcluster

and wait until the command terminates.

### Post install

[Check the tutorial](/docs/tutorial/) to learn how to use it.

To uninstall the development cluster, execute the command:

    ops setup devcluster --uninstall
