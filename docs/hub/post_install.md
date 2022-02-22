---

copyright:
years: 2021
lastupdated: "2021-02-20"

---

{:new_window: target="blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}
{:child: .link .ulchildlink}
{:childlinks: .ullinks}

# Post installation configuration

## Prerequisites

* [**jq** ](https://stedolan.github.io/jq/download/){:target="_blank"}{: .externalLink}
* [**git** ](https://git-scm.com/downloads){:target="_blank"}{: .externalLink}
* [**docker** ](https://docs.docker.com/get-docker/){:target="_blank"}{: .externalLink} version 1.13 or greater
* **make**

## Installation verification

1. Complete the steps in [Install {{site.data.keyword.ieam}}](online_installation.md)


## Post installation configuration
{: #postconfig}

The following process must run on a host that supports installation of the **hzn** CLI, which currently can be installed on a Debian/apt based Linux, amd64 Red Hat/rpm Linux, or macOS host. These steps use the same media downloaded from PPA in the Installation verification section.

## What's Next

After completing the steps in [Post installation configuration](#postconfig), follow the process on the [Gather edge node files](gather_files.md) page to prepare installation media for your edge nodes.
