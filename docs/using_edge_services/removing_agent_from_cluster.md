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

# Removing agent from edge cluster
{: #remove_agent}

To unregister an edge cluster and remove the {{site.data.keyword.ieam}} agent from that cluster, perform these steps:

1. Extract the **agent-uninstall.sh** script from the tar file:

   ```bash
   tar -zxvf agentInstallFiles-x86_64-Cluster.tar.gz agent-uninstall.sh
   ```
   {: codeblock}

2. Export your Horizon exchange user credentials:

   ```bash
   export HZN_EXCHANGE_USER_AUTH=iamapikey:<api-key>
   ```
   {: codeblock}

3. Remove the agent:

   ```bash
   ./agent-uninstall.sh -u $HZN_EXCHANGE_USER_AUTH -d
   ```
   {: codeblock}

Note: Occasionally, deleting the namespace stalls in the "Terminating" state. In this situation, see [A namespace is stuck in the Terminating state ](https://www.ibm.com/support/knowledgecenter/SSBS6K_3.1.1/troubleshoot/ns_terminating.html){:target="_blank"}{: .externalLink} to manually delete namespace.