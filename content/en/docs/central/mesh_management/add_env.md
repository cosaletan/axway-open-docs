---
title: Add your environment to AMPLIFY Central
linkTitle: Add your environment to AMPLIFY Central
weight: 146
date: 2020-03-18
description: Learn how to add your environments to AMPLIFY Central so that you
  can manage your microservices and any related APIs they expose.
---
{{< alert title="Public beta" color="warning" >}}This feature is currently in **public beta** and not yet available for production use.{{< /alert >}}

## Before you start

* Read [AMPLIFY Central mesh governance overview](/docs/central/mesh_management).
* You will need a private cloud Kubernetes cluster that meets the minimum requirements for an AMPLIFY Central hybrid environment, and a client system from which you can access and manage the cluster remotely. See [Build your hybrid environment](/docs/central/mesh_management/build_hybrid_env).
* You will need a basic understanding of OAuth authorization ([RFC 6749](https://tools.ietf.org/html/rfc6749)) and JWT ([RFC 7523](https://tools.ietf.org/html/rfc7523)).
* You must be familiar with Kubernetes and Helm, including running Helm and kubectl commands.
* You will need an administrator account for AMPLIFY Central.

## Objectives

Learn how to add your private cloud hybrid environment to AMPLIFY Central, so that you can manage your microservices and any related APIs they expose, from AMPLIFY Central in AMPLIFY Central public cloud.

* Add your Kubernetes environment to AMPLIFY Central
* Download the generated hybrid kit for your Kubernetes environment
* Generate a key pair and secret for the domain edge gateway and deploy it into the Istio namespace
* Generate a key pair and secret for the Axway mesh agents and deploy them into the mesh agent namespace
* Deploy the Axway proprietary service mesh layer into your environment
* Create and test an API proxy for the API exposed by a demo microservice

## Add your environment to AMPLIFY Central

Follow these steps to add your environment to AMPLIFY Central:

1. Log in to AMPLIFY Central UI as an administrator.
2. Navigate to the **Topology** section using the side navigation bar. (You will see a list of your environments that are managed in AMPLIFY Central.)
3. Click **+ Environment** at the top right.

![Environments List Page](/Images/central/environments_list_page.png)

## Add your Kubernetes environment

To add an AMPLIFY Central environment for your private cloud Kubernetes cluster:

1. Click **Kubernetes** on the **Add a New Environment** page.
2. Enter your environment details.

   * **Name**: Provide a friendly name describing your kubernetes service mesh environment.
   * **Runtime name**: Provide a unique name for the ingress gateway Mesh Governance will add to your Kubernetes cluster.
   * **Protocol**: Select the gateway protocol (HTTP or HTTPS) to use for the ingress  gateway. For HTTPS, you must provide a certificate for the domain.
   * **Host**: Provide the publicly available domain name for your kubernetes cluster (for example, `mydomain.com`). For HTTPS, you must own or be able to configure a certificate for this domain.
   * **Port**: Provide the port number to expose your ingress gateway.
3. Click **Save**.

{{< alert title="Note" color="" >}} The value in the **Host** field must use the same FQDN used in the [Generate a key pair and secret for the domain edge gateway](#generate-key-pairs-and-secrets-for-the-axway-mesh-agents) step to define your hybrid environment.{{< /alert >}}

![Add K8 Environment](/Images/central/completed_k8s_form.png)

After your Kubernetes environment is created, a dialog box is shown with a successful message and the options to either connect to your newly created environment or go back to the environments list page.

Clicking **Connect environment** takes you to your Kubernetes environment details page. There, you can download an auto-generated hybrid kit specific to your environment. The hybrid kit contains Helm chart configuration that will be used later to deploy the Axway proprietary service mesh layer to your environment.

Download the hybrid kit to your client system and unzip it to a unique directory. For example:

```
ls *.zip
e4fd7216693f50360169492633ab0122-override.zip
unzip e4fd7216693f50360169492633ab0122-override.zip -d e4fd7216693f50360169492633ab0122
cd e4fd7216693f50360169492633ab0122
ls
hybridOverride.yaml  istioOverride.yaml
```

### Generate a key pair and secret for the domain edge gateway

To expose an HTTPS endpoint of a service within your environment to external traffic, you need a public certificate and private key for the domain where your environment is hosted, and a TLS secret based on the key pair.

1. Create an X.509 certificate and key for your domain (for example, using [Let's Encrypt](https://letsencrypt.org/)).

   * The domain certificate must match the domain (FQDN) of your environment.
   * The public key certificate must be PEM encoded and match the given private key.
2. Create the Istio namespace. This is the namespace where Istio will be deployed.

   Usage: `kubectl create namespace NAMESPACE_NAME`

   The default value of `NAMESPACE_NAME` is `istio-system`, and this value is used later when the helm upgrade deployment steps are executed in [Deploy the service mesh and Axway mesh agents](#deploy-the-service-mesh-and-axway-mesh-agents).

   Example:

   ```
   kubectl create namespace istio-system
   namespace/istio-system created
   ```
3. Create a Kubernetes TLS secret to hold the public certificate and private key, and deploy it into the Istio namespace.

   Usage: `kubectl create secret tls SECRET_NAME -n NAMESPACE_NAME --key /PATH/TO/KEY/FILE --cert /PATH/TO/CERT/FILE`

   `SECRET_NAME` must match the field `secretName` in the `istioOverride.yaml` Helm chart that you downloaded from AMPLIFY Central as part of the hybrid kit. The `secretName` in the Helm chart  is generated from your domain name, for example, `kubernetes-cluster-example-certs` for the domain `kubernetes-cluster.example.com`.

   Example:

   ```
   kubectl create secret tls kubernetes-cluster-example-certs -n istio-system --key kubernetes-cluster.example.com.key.pem --cert kubernetes-cluster.example.com.cert.pem
   secret/kubernetes-cluster-example-certs created
   kubectl get secrets -n istio-system
   NAME                               TYPE                                  DATA   AGE
   kubernetes-cluster-example-certs   kubernetes.io/tls                     2      4m
   default-token-jvw9m                kubernetes.io/service-account-token   3      27m
   ```

### Generate key pairs and secrets for the Axway mesh agents

Before you can deploy the Axway mesh agents in your environment, you must generate key pairs and make those keys available to Kubernetes in the namespace where the agents will be deployed. For more information on Axway mesh agents, see [Axway mesh agents](/docs/central/mesh_management/#axway-mesh-agents).

#### What are these keys used for?

Mesh agents use service accounts when communicating from your environment to the AMPLIFY Central SaaS control plane. A service account is provisioned in AMPLIFY Central when you create a new environment in the AMPLIFY Central UI. When a mesh agent starts for the first time it uses a one-time credential to authenticate itself to AMPLIFY Central. The agent registers a public key with AMPLIFY Central, and the agent must have access to the associated private key during registration.

AMPLIFY Central uses the public key to authenticate signed JWT tokens from the agent. The agent signs the JWT token with its private key, and the agent private key never leaves your environment. This registration step uses a one-time registration access token (`registrationToken`) that is contained in the `hybridOverride.yaml` Helm chart that you downloaded from AMPLIFY Central as part of the hybrid kit.

#### Generate key pair for SDA

Use `openssl` to generate a public certificate and private key for the service discovery agent (SDA) . The following example uses the password `changeme`:

```
mkdir sdacerts
cd sdacerts/
echo -n "changeme" > password
openssl genrsa -des3 -out private_key.pem -passout file:password 2048
Generating RSA private key...
openssl rsa -pubout -in  private_key.pem -passin file:password -out public_key.der -outform der && base64 public_key.der > public_key
writing RSA key
```

#### Generate key pair for CSA

Use `openssl` to generate a public certificate and private key for the configuration synchronization agent (CSA):

```
mkdir csacerts
cd csacerts/
openssl genpkey -algorithm RSA -out private_key.pem -pkeyopt rsa_keygen_bits:2048
...
openssl rsa -pubout -in private_key.pem -out public_key.der -outform der && base64 public_key.der > public_key
writing RSA key
```

#### Create namespace for mesh agents

Create the namespace where the Axway mesh agents will be deployed.

Usage: `kubectl create namespace NAMESPACE_NAME`

The default value for `NAMESPACE_NAME` is `apic-control` and this value is used later when the helm upgrade deployment steps are executed in [Deploy the service mesh and Axway mesh agents](#deploy-the-service-mesh-and-axway-mesh-agents).

Example:

```
kubectl create namespace apic-control
namespace/apic-control created
```

#### Create and deploy Kubernetes secrets

As the mesh agents are running within Kubernetes, you must create Kubernetes secrets for the agents' public certificates and private keys, and store them in the secret store so that the mesh agents can locate them.

Create Kubernetes secrets to hold the mesh agents' public certificates and private keys, and deploy them in the mesh agent namespace:

Usage: `kubectl create secret generic SECRET_NAME --namespace NAMESPACE_NAME --from-file=publicKey=/PATH/TO/PUBLIC/KEY/FILE --from-file=privateKey=/PATH/TO/PRIVATE/KEY/FILE --from-file=password=PASSWORD_FILE --from-literal=password=PASSWORD -o yaml`

* Each `SECRET_NAME` must match the corresponding SDA or CSA field `keysSecretName` in the `hybridOverride.yaml` Helm chart that you downloaded from AMPLIFY Central as part of the hybrid kit.
* The SDA default value of `keysSecretName` is `sda-secrets`.
* The CSA default value of `keysSecretName` is `csa-secrets`.
* To change the secret store names, edit the `keysSecretName` values in the `hybridOverride.yaml` file before you execute the helm upgrade deployment steps.

Example for SDA:

```
cd sdacerts/
kubectl create secret generic sda-secrets --namespace apic-control --from-file=publicKey=public_key --from-file=privateKey=private_key.pem --from-file=password="password" -o yaml
```

Example for CSA:

```
cd csacerts/
kubectl create secret generic csa-secrets --namespace apic-control --from-file=publicKey=public_key --from-file=privateKey=private_key.pem --from-literal=password="" -o yaml
```

Verify that the secrets are deployed in the `apic-control` namespace:

```
kubectl get secrets -n apic-control
NAME                  TYPE                                  DATA   AGE
csa-secrets           Opaque                                3      29s
default-token-f26bp   kubernetes.io/service-account-token   3      4m
sda-secrets           Opaque                                3      3m
```

### Deploy the service mesh and Axway mesh agents

After you have created the key pairs and secrets, deploy the Axway proprietary service mesh into your environment:

1. To ensure that you have the latest Axway Helm charts, update your Helm repositories:

   ```
   helm repo up
   Hang tight while we grab the latest from your chart repositories...
   ...Skip local chart repository
   ...Successfully got an update from the "axway" chart repository
   Update Complete. ⎈ Happy Helming!⎈
   ```
2. Change to the directory where you unzipped the hybrid kit:

   ```
   cd e4fd7216693f50360169492633ab0122/
   ```
3. From Istio 1.6+, `Istioctl` is required for installing Istio. If you have not previously added it to your client system, you can download `Istioctl` using the following command:

   ```
   curl -L https://istio.io/downloadIstio | ISTIO_VERSION=1.6.8 TARGET_ARCH=x86_64 sh -
   ```
4. Export the path where you downloaded `Istioctl` to start using it. Note the use of `$PWD` below and change it accordingly.

   ```
   export PATH=$PATH:$PWD/istio-1.6.8/bin
   ```
5. Install Istio using `Istioctl`. This step can take several minutes to complete.

   ```
   istioctl install --set profile=demo -f <pathtooverridefile>/istioOverride.yaml

    ✔ Istio core installed
    ✔ Istiod installed  
    ✔ Policy installed
    ✔ Ingress gateways installed
    ✔ Telemetry installed
    ✔ Addons installed
    ✔ Installation complete
   ```
6. Verify that Istio is deployed successfully:

   ```
   kubectl get services -n istio-system
   ```

   The output of this command should list an domain edge gateway and a number of Istio services.
7. Deploy the Axway mesh agents. This step can take several minutes to complete.

   Usage: `helm upgrade --install --namespace NAMESPACE_NAME RELEASE CHART -f /PATH/TO/OVERRIDE/VALUES [OPTIONS]`

   Example:

   ```
   helm upgrade --install --namespace apic-control apic-hybrid axway/apicentral-hybrid -f ./hybridOverride.yaml --set als.enabled=true

   Release "apic-hybrid" does not exist. Installing it now.
   NAME:   apic-hybrid
   LAST DEPLOYED: Wed Aug  5 20:25:53 2020
   NAMESPACE: apic-control
   STATUS: DEPLOYED

   RESOURCES:
   ==> v1/Deployment
   NAME             AGE
   apic-hybrid-als  2s

   ==> v1/Pod(related)
   NAME                               AGE
   apic-hybrid-als-7459c5578b-98fnn   2s
   apic-hybrid-als-7459c5578b-zhjmh   2s
   apic-hybrid-csa-787c48c7c4-5gqsk   2s
   apic-hybrid-list-598f8f9b4b-zvp4r  2s
   apic-hybrid-sda-64946c45b9-kw5m2   2s

   ==> v1/Service
   NAME              AGE
   apic-hybrid-als   2s
   apic-hybrid-csa   2s
   apic-hybrid-list  2s

   ==> v1/ServiceAccount
   NAME             AGE
   apic-hybrid-als  3s
   apic-hybrid-csa  3s
   apic-hybrid-sda  3s

   ==> v1alpha3/EnvoyFilter
   NAME                                 AGE
   patch-gateway-and-sidecars-with-als  2s
   patch-gateway-and-sidecars-with-lua  2s
   patch-gateway-route-config           2s
   patch-inbound-route-config           2s
   patch-outbound-route-config          2s

   ==> v1beta1/ClusterRole
   NAME             AGE
   apic-hybrid-csa  3s
   apic-hybrid-sda  3s

   ==> v1beta1/ClusterRoleBinding
   NAME             AGE
   apic-hybrid-csa  2s
   apic-hybrid-sda  3s

   ==> v1beta1/CustomResourceDefinition
   NAME                  AGE
   syncpoints.axway.com  3s

   ==> v1beta2/Deployment
   NAME              AGE
   apic-hybrid-csa   2s
   apic-hybrid-list  2s
   apic-hybrid-sda   2s
   ```
8. Verify that the mesh agents are deployed in the `apic-control` namespace:

   ```
    kubectl get services -n apic-control
   ```

    The output of this command should list the mesh agent services.
9. Verify that the list demo service is deployed in the `apic-demo` namespace:

   ```
   kubectl get services -n apic-demo
   ```

   The output of this command should list the demo list service.
10. Verify that your environment is now connected in AMPLIFY Central UI:

    ![Connected environment in AMPLIFY Central](/Images/central/hybrid__env_connected.png)

### Create and test an API proxy for the demo service

The list demo service is now deployed in your hybrid environment. You can create an API proxy for the API exposed by the demo service, in much the same way as you would for any other API. Watch the short animation to learn how.

![Create a proxy for API exposed by microservice](/Images/central/hybrid_env_proxy_demo.gif)

You can test the API proxy in AMPLIFY Central UI or using a REST client. The following example uses `curl` to send a `GET` request to the `/list` endpoint:

Example:

```
curl -is -X GET 'http://kubernetes-cluster.example.com:8080/listapi/list'
HTTP/1.1 200 OK
server: envoy
request-id: c14a76d6-5ec6-4831-9650-e8a11258edbb
start-time: 1552319365217
content-type: application/json; charset=utf-8
response-time: 2
content-md5: 8e9f6888c977fa5494181d57aeb9d242
content-length: 152
etag: W/"98-umYiZYKr43H9eDmrYoXeehz5zHw"
vary: Accept-Encoding
date: Mon, 11 Mar 2019 15:49:24 GMT
x-envoy-upstream-service-time: 5

[{"id":1,"name":"dog","price":"20","store":"pet"},
  {"id":2,"name":"cat","price":"30","store":"pet"},
  {"id":3,"name":"monkey","price":"600","store":"zoo"}]
```

## Review

You have learned how to add your private cloud hybrid environment to AMPLIFY Central, and how to create and test an API proxy for an API exposed by a demo microservice deployed in the hybrid environment.