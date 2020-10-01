---
title: Build your hybrid environment
linkTitle: Build your hybrid environment
weight: 145
date: 2019-07-30T00:00:00.000Z
description: Learn how to build a basic Amazon EC2 private cloud hybrid
  environment and add the required tools to enable you to access and manage it
  remotely from a client system.
---
{{< alert title="Public beta" color="warning" >}}This feature is currently in **public beta** and not yet available for production use.{{< /alert >}}

## Before you start

* Read [AMPLIFY Central mesh governance overview](/docs/central/mesh_management/).
* You will need a basic knowledge of Amazon Web Services (AWS), Amazon EC2 instances, and associated tools.
* You will need to be familiar with Kubernetes and Helm, including running Helm, kubectl, and kops commands.

## Objectives

Learn how to build a basic Amazon EC2 private cloud hybrid environment and add the required tools to enable you to access and manage it remotely from a client system.

{{< alert title="Tip" color="" >}}For the latest Amazon EC2 build instructions and more client system configuration details, see [Set up AMPLIFY Central mesh governance documentation on GitHub](https://github.com/Axway/Setup-Amplify-Mesh-Governance).{{< /alert >}}

## Minimum requirements

* Amazon EC2 instance with Kubernetes and Helm:

    * Kubernetes 1.15 is supported
    * Helm 2.16.7 or later (but not 3.x) is recommended

* Public facing fully qualified domain name (FQDN) of the Amazon EC2 cluster
* Client system (for example, Linux VM) with the following tools installed for accessing and managing your Amazon EC2 environment remotely:

    * AWS CLI 1.16 or later is recommended - Enables you to interact with AWS services from the command line. See the [AWS CLI installation documentation](https://docs.aws.amazon.com/cli/latest/userguide/li-chap-install.html).
    * kubectl 1.16 or later is recommended - Enables you to deploy and manage applications on Kubernetes from the command line. See the [kubectl installation documentation](https://kubernetes.io/docs/tasks/tools/install-kubectl/).
    * kops 1.16 or later is recommended - Helps you create, destroy, upgrade and maintain Kubernetes clusters from the command line. See the [kops installation documentation](https://github.com/kubernetes/kops/blob/master/docs/install.md).
    * Helm 2.16.7+ (but not 3.x) is recommended - Enables you to install the Axway proprietary service mesh layer later and to export Helm charts. See the [Helm installation documentation](https://helm.sh/docs/using_helm/#installing-helm).
    * Istioctl - Used after the environment is built for the next phase to deploy the service mesh and add this environment to AMPLIFY Central.

## Build an Amazon EC2 hybrid environment

Follow these guidelines to build a basic Amazon EC2 hybrid environment manually.

### Set up Amazon EC2

1. To create an Amazon EC2 environment, follow the five steps in the [Amazon EC2 set up documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html).
2. To launch and connect to an Amazon EC2 instance, follow the first two steps in the [Amazon EC2 get started documentation](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html). When launching the instance, choose a Linux AMI and an instance type of `t2.large`.

### Add Kubernetes to Amazon EC2

To install a Kubernetes cluster on AWS using kops, follow the steps in the [Kubernetes installation on AWS documentation](https://kubernetes.io/docs/setup/custom-cloud/kops/).

### Access the Amazon EC2 cluster from your client

When the cluster is created, use kops to export the configuration from the Amazon EC2 cluster to your client system. This updates a file in your home directory (`~/.kube/config`) with information about contacting the cluster, which allows you to interact with your cluster from your client using kubectl, Helm, and so on.

Usage:

```
kops export kubecfg --name CLUSTER_NAME --state STATE_STORAGE_LOCATION
```

Example:

```
kops export kubecfg --name kubernetes-cluster.example.com --state s3://amazonaws.com
```

* `kubernetes-cluster.example.com` is the public FQDN (or name) of your cluster
* `s3://amazonaws.com` is the Amazon S3 cloud storage bucket location defined when Kubernetes was added to your cluster

### Configure Helm on Amazon EC2

Install Helm on your cluster and add the Axway public repository to Helm:

1. To install the Helm server (Tiller) on the connected cluster:

   ```
   helm init
   ```
2. Verify the Helm version:

   ```
   helm version
   Client: &version.Version{SemVer:"v2.13.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b146b", GitTreeState:"clean"}
   Server: &version.Version{SemVer:"v2.13.0", GitCommit:"2e55dbe1fdb5fdb96b75ff144a339489417b146b", GitTreeState:"clean"}
   ```
3. Add the Axway public Helm repository to your installation:

   ```
   helm repo add axway https://charts.axway.com/charts
   "axway" has been added to your repositories
   ```
4. Verify that the Axway public repository has been added:

   ```
   helm repo list
   NAME            URL
   stable          https://kubernetes-charts.storage.googleapis.com
   local           http://127.0.0.1:8879/charts
   axway           https://charts.axway.com/charts
   ```

## Validate the Amazon EC2 hybrid environment

Use the following commands to validate your environment using kubectl or kops.

kubectl:

```
    kubectl get nodes
    NAME                                         STATUS   ROLES    AGE   VERSION
    ip-172-0-33-242.us-west-2.compute.internal   Ready    node     25d   v1.10.12
    ip-172-0-35-225.us-west-2.compute.internal   Ready    node     25d   v1.10.12
    ip-172-0-59-93.us-west-2.compute.internal    Ready    master   25d   v1.10.12
    ip-172-0-70-60.us-west-2.compute.internal    Ready    node     25d   v1.10.12
```

kops:

```
kops validate cluster
Using cluster from kubectl context: kubernetes-cluster.example.com
...
Your cluster kubernetes-cluster.example.com is ready
```

## Review and next steps

You have learned how to build a basic Amazon EC2 hybrid environment and add the required tools to enable you to access and manage it from your client system. Next, read [Add your environment to AMPLIFY Central](/docs/central/mesh_management/add_env) to learn how to add your environment to AMPLIFY Central.