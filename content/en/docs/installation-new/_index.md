---
title: Installation-new
description: How to install OpenServerless
weight: 80
---

Slogan: OpenServerless is ...

The audience of this document is composed by developers, SREs, and OpenServerless contributors.


## Prepare the target environment on which you want to install OpenServerless

TODO 1: PUT A SCHEMA HERE
TODO 2: FOR EACH EXTERNAL LINK, ADD A TARGET _blank TO OPEN THE PAGE IN A NEW BROWSER TAB.


Here we illustrate how to prepare a target environment for OpenServerless. 

Depending on your use case, you can install OpenServerless:

- for *development* on a [single node](#single-node-development-installation) environment,
    either on your local machine or on a Linux server.

- for *production*, on a [multi node](#multi-node-production-installation) environment
    provided by a Kubernetes cluster.

### Single Node development installation

For *development* purposes, you can install a single node
OpenServerless deployment on:

- your [local machine](/docs/installation-new/install/local/), you need
    [Docker Desktop](/docs/installation-new/prereq/docker/)

- on a *single Linux server node*
  [Linux server](/docs/installation-new/install/server/), you need a server with [passwordless ssh access and sudo](/docs/installation-new/prereq/server/).

The OpenServerless `ops` CLI can automatically install a Kubernetes environment, using
[K3S](https://k3s.io), but if you prefer, you can install a single-node
Kubernetes instance by yourself.

If you choose to install Kubernetes on your server, we provide support
for:

- [SuSE K3S](/docs/installation-new/prereq/k3s/)

- [Canonical MicroK8S](/docs/installation-new/prereq/mk8s)

### Multi Node production installation

For *production* purposes, you need a multi-node Kubernetes cluster
that satisfies [those prerequisites](/docs/installation-new/prereq/kubernetes/),
 with its `kubeconfig` file accessible.

If you have such a cluster, you can install
[OpenServerless in a Kubernetes cluster](/docs/installation-new/install/cluster/)

If you do not have a cluster and you need to setup one, we provide
support for provisioning a suitable cluster that satisfied our
prerequisites for the following Kubernetes environments:

- [EKS in Amazon AWS](/docs/installation-new/prereq/kubernetes/eks/)

- [AKS in Microsoft Azure](/docs/installation-new/prereq/kubernetes/aks/)

- [GKE in Google Cloud](/docs/installation-new/prereq/kubernetes/gke/)

- [RedHat OpenShift](/docs/installation-new/prereq/kubernetes/openshift/)

Once you have a suitable Kubernetes cluster, you can proceed with the next chapter.

## Installation

Before starting the installation, please be sure to have satisfied the prerequisites shown above.

OpenServerless can be installed in many target environments, using the `ops` CLI (Command Line Interface).
Once you have installed the `ops` CLI, you can configure Openserverless on the target environment.

To perform the installation follow this step-by-step procedure:

1. Download and install the `ops` CLI (Command Line Interface) for the computer you are working on.
2. Install the required packages using the `ops` CLI on the target environment that you prepared in the prerequistes section above.


## Configure OpenServerless

TODO REVIEW THIS PART

- [configure](/docs/installation-new/configure/) the services you want


### Download and install the `ops` CLI (Command Line Interface)

The OpenServerless `ops` CLI (Command Line Interface) can be installed on Windows, macOS and Linux.

Three tabs:
- TAB To install on Windows ...
- TAB To install on macOS ...
- TAB To install on Linux ...


{{< tabpane >}}
{{< tab header="Where to install the ops cli :" disabled=true />}}
{{< tab header="Windows" lang="text" >}}
+++
title = "How to install the ops cli on Windows"
weight = 1
type = "docs"
description = '''
A special section with a docs layout.
This is the second line for Windows.
'''
+++
{{< /tab >}}
{{< tab header="macOS" lang="text" >}}
---
title: "How to install the ops cli on macOS"
weight: 1
type: docs
description: >
  A special section with a docs layout for macOS.
  This is the second line.
---
{{< /tab >}}
{{< tab header="Linux" lang="text" >}}
{
  "title": "How to install the ops cli on Linux",
  "weight": 1,
  "type": "docs",
  "description": "A special section with a docs layout for Linux.\n This is the second line for Linux"
}
{{< /tab >}}
{{< /tabpane >}}


### Installation and configuration of OpenServerless on the target environment

To install OpenServerless on the target environment

you can choose where to install OpenServerless, either:

- on your [Local machine](/docs/installation-new/install/local/)

- on a [Linux server](/docs/installation-new/install/server/)

- on a [Kubernetes cluster](/docs/installation-new/install/cluster/)


#### The target is the local machine

This page describes how to install OpenServerless on your local machine. The
services are limited and not accessible from the outside, so this installation is useful only for development purposes.

##### Prerequisites

TODO: remove this chapter?

Before installing, you need to:

- install [Docker](/docs/installation-new/prereq/docker/).

- install [ops](/docs/installation-new/download/).

- [configure](/docs/installation-new/configure/) the services you want

> 💡 **NOTE**
>
> ``https`` protocol and static publishing in a local
installation are not supported. If you enable them, the configuration will be ignored.

##### Installation

Run the command:

    ops setup devcluster

and wait until the command terminates.

##### Post installation

[Check the tutorial](/docs/tutorial/) to learn how to use it.


##### Uninstalling

To uninstall the development cluster, execute the command:

    ops setup devcluster --uninstall



#### The target is the a server
This page describes how to install OpenServerless on a Linux server
accessible with SSH.

This is a single node installation, so it is advisable only for
development purposes.

##### Prerequisites

TODO: remove this chapter?

Before installing, you need to:

1. install the OpenServerless CLI [ops](/docs/installation/download/);

2. provision a [server running a Linux operating system](/docs/installation/prereq/server/), 
   either a virtual machine or a physical server, and you know its IP address 
   or DNS name;

3. configure it to have [passwordless ssh access and sudo rights](/docs/installation/prereq/server/generic/);

4. open the firewall to have access to ports 80, 443 and 6443 or 16443
    from your client machine;

5. [configure](/docs/installation/configure/dns/) the DNS name for the server and choose
    the services you want to enable;

##### Installation

If the prerequisites are satisfied, execute the dommand:

    ops setup server <server> <user>

> ❗ **IMPORTANT**
>
> Replace in the command before `<server>` with the IP address or DNS name
used to access the server, and `<user>` with the username you have to
use to access the server

Wait until the command completes and you will have OpenServerless up and
running.

##### Post Installation

- [Check the tutorial](/docs/tutorial/) to learn how to use it.


##### Uninstalling

- To uninstall, execute the command:

```
ops setup server <server> <user> --uninstall
```


#### The target is a cluster
This section describes how to install OpenServerless on a Kubernetes Cluster

##### Prerequisites

TODO: remove this chapter?


Before installing, you need to:

- [Provision](/docs/installation/prereq/kubernetes/) a Kubernetes Cluster

- [Configure](/docs/installation/configure/) the installation

- install [Download and install](/docs/installation/download/) OpenServerless CLI, `ops`.

##### Installation

If you have a Kubernetes cluster directly accessible with its
configuration, or you provisioned a cluster in some cloud using `ops`
embedded tools, you just need to type:

    ops setup cluster

Sometimes the kubeconfig includes access to multiple Kubernetes
instances, each one identified by a different `<context>` name. You can
install the OpenServerless cluster in a specified `<context>` with:

    ops setup cluster <context>

##### Post Install

- [Check the tutorial](/docs/tutorial) to learn how to use it.


##### Uninstalling

To uninstall, execute the command:

```
ops setup cluster --uninstall
```


### Support

If you have issues, please check:

- the [Troubleshooting](/docs/installation-new/debug/) page

- our [Discussion forum](https://nuvolaris.discourse.group)
