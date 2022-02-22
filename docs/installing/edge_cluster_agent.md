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

# Installing the agent
{: #importing_clusters}

**Note**: {{site.data.keyword.ieam}} agent installation requires cluster admin access on the edge cluster.

Begin by installing the {{site.data.keyword.edge_notm}} agent on one of these types of Kubernetes edge clusters:

* [Installing agent on {{site.data.keyword.ocp}} Kubernetes edge cluster](#install_kube)
* [Installing agent on k3s and microk8s edge clusters](#install_lite)

Then, deploy an edge service to your edge cluster:

* [Deploying services to your edge cluster](#deploying_services)

If you need to remove the agent:

* [Removing agent from edge cluster](../using_edge_services/removing_agent_from_cluster.md)

## Installing agent on {{site.data.keyword.ocp}} Kubernetes edge cluster
{: #install_kube}

This content describes how to install the {{site.data.keyword.ieam}} agent on your {{site.data.keyword.ocp}} edge cluster. Follow these steps on a host that has admin access to your edge cluster:

1. Log in to your edge cluster as **admin**:

   ```bash
   oc login https://<api_endpoint_host>:<port> -u <admin_user> -p <admin_password> --insecure-skip-tls-verify=true
   ```
   {: codeblock}

2. If you have not completed the steps in [Prepare for setting up edge nodes](../hub/prepare_for_edge_nodes.md), do that now. This process creates an API key, locates some files, and gathers environment variable values that are needed when you set up edge nodes. Set the same environment variables on this edge cluster:

  ```bash
  export HZN_EXCHANGE_USER_AUTH=iamapikey:<api-key>
  export HZN_ORG_ID=<your-exchange-organization>
  export HZN_FSS_CSSURL=https://<ieam-management-hub-ingress>/edge-css/
  ```
  {: codeblock}

3. Set the agent namespace variable to its default value (or whatever namespace you want to explicitly install the agent into):

   ```bash
   export AGENT_NAMESPACE=openhorizon-agent
   ```
   {: codeblock}

4. Set the storage class that you want the agent use to either a built-in storage class or one you created. For example:

   ```bash
   export EDGE_CLUSTER_STORAGE_CLASS=rook-ceph-cephfs-internal
   ```
   {: codeblock}

5. To set up the image registry on your edge cluster, complete steps 2 - 8 of [Using the OpenShift image registry](../developing/container_registry.md##ocp_image_registry) with this one change: In step 4, set **OCP_PROJECT** to: **$AGENT_NAMESPACE**.

6. The **agent-install.sh** script stores the {{site.data.keyword.ieam}} agent in the edge cluster container registry. Set the registry user, password, and full image path (minus the tag):

   ```bash
   export EDGE_CLUSTER_REGISTRY_USERNAME=$OCP_USER
   export EDGE_CLUSTER_REGISTRY_TOKEN="$OCP_TOKEN"
   export IMAGE_ON_EDGE_CLUSTER_REGISTRY=$OCP_DOCKER_HOST/$OCP_PROJECT/amd64_anax_k8s
   ```
   {: codeblock}

   **Note:** The {{site.data.keyword.ieam}} agent image is stored in the local edge cluster registry because the edge cluster Kubernetes needs ongoing access to it, in case it needs to restart it or move it to another pod.

7. Copy the **agent-install.sh** script to your new edge cluster.

8. Run **agent-install.sh** to get the necessary files from CSS (Cloud Sync Service), install and configure the {{site.data.keyword.horizon}} agent, and register your edge cluster with policy:

   ```bash
   ./agent-install.sh -D cluster -i 'css:'
   ```
   {: codeblock}

   **Notes:**
   * To see all of the available flags, run: **./agent-install.sh -h**
   * If an error causes **agent-install.sh** to fail, correct the error and run **agent-install.sh** again. If that does not work, run **agent-uninstall.sh** (see [Removing agent from edge cluster](../using_edge_services/removing_agent_from_cluster.md)) before running **agent-install.sh** again.

9. Change to the agent namespace (also known as project) and verify that the agent pod is running:

   ```bash
   oc project $AGENT_NAMESPACE
   oc get pods
   ```
   {: codeblock}

10. Now that the agent is installed on your edge cluster, you can run these commands if you want to familiarize yourself with the Kubernetes resources associated with the agent:

   ```bash
   oc get namespace $AGENT_NAMESPACE
   oc project $AGENT_NAMESPACE   # ensure this is the current namespace/project
   oc get deployment -o wide
   oc get deployment agent -o yaml   # get details of the deployment
   oc get configmap openhorizon-agent-config -o yaml
   oc get secret openhorizon-agent-secrets -o yaml
   oc get pvc openhorizon-agent-pvc -o yaml   # persistent volume
   ```
   {: codeblock}

11. Often, when an edge cluster is registered for policy, but does not have user-specified node policy, none of the deployment policies will deploy edge services to it. That is the case with the Horizon examples. Proceed to [Deploying services to your edge cluster](#deploying_services) to set node policy so that an edge service will be deployed to this edge cluster.

## Installing agent on k3s and microk8s edge clusters
{: #install_lite}

This content describes how to install the {{site.data.keyword.ieam}} agent on [k3s ](https://k3s.io/){:target="_blank"}{: .externalLink} or [microk8s ](https://microk8s.io/){:target="_blank"}{: .externalLink}, lightweight and small Kubernetes clusters:

1. Log in to your edge cluster as **root**.

2. If you have not completed the steps in [Prepare for setting up edge nodes](../hub/prepare_for_edge_nodes.md), do that now. This process creates an API key, locates some files, and gathers environment variable values that are needed when setting up edge nodes. Set the same environment variables on this edge cluster:

  ```bash
  export HZN_EXCHANGE_USER_AUTH=iamapikey:<api-key>
  export HZN_ORG_ID=<your-exchange-organization>
  export HZN_FSS_CSSURL=https://<ieam-management-hub-ingress>/edge-css/
  ```
  {: codeblock}

3. Copy the **agent-install.sh** script to your new edge cluster.

4. The **agent-install.sh** script will store the {{site.data.keyword.ieam}} agent in the edge cluster image registry. Set the full image path (minus the tag) that should be used. For example:

   * On k3s:

      ```bash
      REGISTRY_ENDPOINT=$(kubectl get service docker-registry-service | grep docker-registry-service | awk '{print $3;}'):5000
      export IMAGE_ON_EDGE_CLUSTER_REGISTRY=$REGISTRY_ENDPOINT/openhorizon-agent/amd64_anax_k8s
      ```
      {: codeblock}

   * On microk8s:

      ```bash
      export IMAGE_ON_EDGE_CLUSTER_REGISTRY=localhost:32000/openhorizon-agent/amd64_anax_k8s
      ```
      {: codeblock}

   **Note:** The {{site.data.keyword.ieam}} agent image is stored in the local edge cluster registry because the edge cluster Kubernetes needs ongoing access to it, in case it needs to restart it or move it to another pod.

5. Instruct **agent-install.sh** to use the default storage class:

   * On k3s:

      ```bash
      export EDGE_CLUSTER_STORAGE_CLASS=local-path
      ```
      {: codeblock}

   * On microk8s:

      ```bash
      export EDGE_CLUSTER_STORAGE_CLASS=microk8s-hostpath
      ```
      {: codeblock}

6. Run **agent-install.sh** to get the necessary files from CSS (Cloud Sync Service), install and configure the {{site.data.keyword.horizon}} agent, and register your edge cluster with policy:

   ```bash
   ./agent-install.sh -D cluster -i 'css:'
   ```
   {: codeblock}

   **Notes:**
   * To see all of the available flags, run: **./agent-install.sh -h**
   * If an error occurs causing **agent-install.sh** to not complete successfully, correct the error that is displayed, and run **agent-install.sh** again. If that does not work, run **agent-uninstall.sh** (see [Removing agent from edge cluster](../using_edge_services/removing_agent_from_cluster.md)) before running **agent-install.sh** again.

7. Verify that the agent pod is running:

   ```bash
   kubectl get namespaces
   kubectl -n openhorizon-agent get pods
   ```
   {: codeblock}

8. Usually, when an edge cluster is registered for policy, but does not have any user-specified node policy, none of the deployment policies deploy edge services to it. This is expected. Proceed to [Deploying services to your edge cluster](#deploying_services) to set node policy so that an edge service will be deployed to this edge cluster.

## Deploying services to your edge cluster
{: #deploying_services}

Setting node policy on this edge cluster can cause deployment policies to deploy edge services here. This content shows an example of doing that.

1. Set some aliases to make it more convenient to run the `hzn` command. (The `hzn` command is inside the agent container, but these aliases make it possible to run `hzn` from this host.)

   ```bash
   cat << 'END_ALIASES' >> ~/.bash_aliases
   alias getagentpod='kubectl -n openhorizon-agent get pods --selector=app=agent -o jsonpath={.items[*].metadata.name}'
   alias hzn='kubectl -n openhorizon-agent exec -i $(getagentpod) -- hzn'
   END_ALIASES
   source ~/.bash_aliases
   ```
   {: codeblock}

2. Verify that your edge node is configured (registered with the {{site.data.keyword.ieam}} management hub):

   ```bash
   hzn node list
   ```
   {: codeblock}

3. To test your edge cluster agent, set your node policy with a property that deploys the example helloworld operator and service to this edge node:

   ```bash
   cat << 'EOF' > operator-example-node.policy.json
   {
     "properties": [
       { "name": "openhorizon.example", "value": "operator" }
     ]
   }
   EOF

   cat operator-example-node.policy.json | hzn policy update -f-
   hzn policy list
   ```
   {: codeblock}

   **Note:**
   * Because the real **hzn** command is running inside the agent container, for any `hzn` commands that require an input file, you need to pipe the file into the command so its content will be transferred into the container.

4. After a minute, check for an agreement and the running edge operator and service containers:

   ```bash
   hzn agreement list
   kubectl -n openhorizon-agent get pods
   ```
   {: codeblock}

5. Using the pod IDs from the previous command, view the log of edge operator and service:

   ```bash
   kubectl -n openhorizon-agent logs -f <operator-pod-id>
   # control-c to get out
   kubectl -n openhorizon-agent logs -f <service-pod-id>
   # control-c to get out
   ```
   {: codeblock}

6. You can also view the environment variables that the agent passes to the edge service:

   ```bash
   kubectl -n openhorizon-agent exec -i <service-pod-id> -- env | grep HZN_
   ```
   {: codeblock}

### Changing what services are deployed to your edge cluster
{: #changing_services}

* To change what services are deployed to your edge cluster, change the node policy:

  ```bash
  cat <new-node-policy>.json | hzn policy update -f-
  hzn policy list
  ```
  {: codeblock}

   After a minute or two, the new services will be deployed to this edge cluster.

* **Note**: On some VMs with microk8s, the service pods that are being stopped (replaced) might stall in the **Terminating** state. If that happens, run:

  ```bash
  kubectl delete pod <pod-id> -n openhorizon-agent --force --grace-period=0
  pkill -fe <service-process>
  ```
  {: codeblock}

* If you want to use a pattern, instead of policy, to run services on your edge cluster:

  ```bash
  hzn unregister -f
  hzn register -n $HZN_EXCHANGE_NODE_AUTH -p <pattern-name>
  ```
  {: codeblock}
