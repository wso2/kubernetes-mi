# Helm Chart for deployment of WSO2 Micro Integrator

## Contents

* [Prerequisites](#prerequisites)
* [Quick Start Guide](#quick-start-guide)

## Prerequisites

* In order to use WSO2 Helm resources, you need an active WSO2 subscription. If you do not possess an active WSO2
  subscription already, you can sign up for a WSO2 Free Trial Subscription from [here](https://wso2.com/free-trial-subscription)
  . Otherwise you can proceed with docker images which are created using GA releases.<br><br>

* Install [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git), [Helm](https://helm.sh/docs/intro/install/)
(and Tiller) and [Kubernetes client](https://kubernetes.io/docs/tasks/tools/install-kubectl/) in order to run the 
steps provided in the following quick start guide.<br><br>

* An already setup [Kubernetes cluster](https://kubernetes.io/docs/setup/).<br><br>

* Install [NGINX Ingress Controller](https://kubernetes.github.io/ingress-nginx/deploy/). Please note that Helm resources for WSO2 product
deployment patterns are compatible with NGINX Ingress Controller Git release [`nginx-0.30.0`](https://github.com/kubernetes/ingress-nginx/releases/tag/nginx-0.30.0).

* Add the WSO2 Helm chart repository.

    ```
     helm repo add wso2 https://helm.wso2.com && helm repo update
    ```
## Quick Start Guide


### Install Chart From [WSO2 Helm Chart Repository](https://hub.helm.sh/charts/wso2)

##### 1. Deploy Helm chart for WSO2 Micro Integrator deployment

- **Helm v2**

    Deploy WSO2 Micro Integrator  using Docker images from WSO2 Private Docker Registry.
    ```
    helm install --name <RELEASE_NAME> wso2/micro-integrator --version 4.2.0-1 --namespace <NAMESPACE> --set wso2.subscription.username=<SUBSCRIPTION_USERNAME> --set wso2.subscription.password=<SUBSCRIPTION_PASSWORD>
    ```

- **Helm v3**
    
    Deploy WSO2 Micro Integrator  using Docker images from WSO2 Private Docker Registry.
    ```
    helm install <RELEASE_NAME> wso2/micro-integrator --version 4.2.0-1 --namespace <NAMESPACE> --set wso2.subscription.username=<SUBSCRIPTION_USERNAME> --set wso2.subscription.password=<SUBSCRIPTION_PASSWORD> --create-namespace
    ```

If you are using a custom WSO2 Docker images you will need to provide those information via the input values. Please refer [Micro Integrator Deployment Configurations](#micro-integrator-deployment-configurations)

**Note:**

* `NAMESPACE` should be the Kubernetes Namespace in which the resources are deployed.

##### 2. Access Micro Integrator.

Default deployment will expose `<RELEASE_NAME>` as the host.

To access the Micro Integrator in the environment,

a. Obtain the external IP (`EXTERNAL-IP`) of the Ingress resources by listing down the Kubernetes Ingresses.

```
kubectl get ing -n <NAMESPACE>
```

```
NAME                                                 HOSTS                      ADDRESS        PORTS     AGE
<RELEASE_NAME>-micro-integrator-service-ingress      mi.wso2.com                <EXTERNAL-IP>  80, 443   3m
<RELEASE_NAME>-micro-integrator-management-ingress   management.mi.wso2.com     <EXTERNAL-IP>  80, 443   3m
```

b. Add the above host as an entry in /etc/hosts file as follows:

```
<EXTERNAL-IP>	mi.wso2.com 
<EXTERNAL-IP>	management.mi.wso2.com 
```


> **Note:** <br>
From the above Helm commands, base image of a Micro Integrator is deployed (without any integration solution). To deploy your integration solution with the Helm charts follow the below steps. <br><br>
>1. [Create an integration service using WSO2 Integration Studio](https://apim.docs.wso2.com/en/latest/integrate/integration-overview). Then [create a Docker image](https://apim.docs.wso2.com/en/latest/integrate/develop/create-docker-project/#creating-docker-exporter) and push it to your private or public Docker registry. <br><br>
    - `INTEGRATION_IMAGE_REGISTRY` will refer to the Docker registry that created Docker image has been pushed <br>
    - `INTEGRATION_IMAGE_NAME` will refer to the name of the created Docker image <br>
    - `INTEGRATION_IMAGE_TAG` will refer to the tag of the created Docker image <br><br>
>2. If your Docker registry is a private registry, [create an imagePullSecret](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/).<br><br>
    - `IMAGE_PULL_SECRET` will refer to the created image pull secret <br><br>
>3. Deploy the helm resource using following command.<br><br>
>   ```
>   helm install <RELEASE_NAME> wso2/micro-integrator --version 4.2.0-1 --namespace <NAMESPACE> --set wso2.deployment.mi.dockerRegistry=<INTEGRATION_IMAGE_REGISTRY> --set wso2.deployment.mi.imageName=<INTEGRATION_IMAGE_NAME> --set wso2.deployment.mi.imageTag=<INTEGRATION_IMAGE_TAG> --set wso2.deployment.mi.imagePullSecrets=<IMAGE_PULL_SECRET>
>   ```     


### Install Chart From Source
>In the context of this document, <br>
>* `KUBERNETES_HOME` will refer to a local copy of the [`wso2/kubernetes-mi`](https://github.com/wso2
>/kubernetes-mi/)
Git repository. <br>
>* `HELM_HOME` will refer to `<KUBERNETES_HOME>/helm/micro-integrator`. <br>

##### 1. Clone the Kubernetes Resources for WSO2 Micro Integrator Git repository.

```
git clone https://github.com/wso2/kubernetes-mi.git
```

##### 2. Provide configurations.

a. The default product configurations are available at `<HELM_HOME>/confs` folder. Change the
configurations as necessary.

b. Open the `<HELM_HOME>/values.yaml` and provide the following values.

###### WSO2 Subscription Configurations

| Parameter                                                                   | Description                                                                               | Default Value               |
|-----------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------|
| `wso2.subscription.username`                                                | Your WSO2 Subscription username                                                           | ""                          |
| `wso2.subscription.password`                                                | Your WSO2 Subscription password                                                           | ""                          |

If you do not have active WSO2 subscription do not change the parameters `wso2.deployment.username`, `wso2.deployment.password`. 

###### Micro Integrator Deployment Configurations

| Parameter                                                                   | Description                                                                               | Default Value               |
|-----------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------|
| `wso2.deployment.mi.dockerRegistry`                                | Docker registry of the micro-integrator image                                                 | ""                          |
| `wso2.deployment.mi.imageName`                                     | Image name for micro-integrator node                                                          | ""                          |
| `wso2.deployment.mi.imageTag`                                      | Image tag for micro-integrator node                                                           | ""                          |
| `wso2.deployment.mi.replicas`                                      | Number of replicas for micro-integrator node                                                  | 1                           |
| `wso2.deployment.mi.strategy.rollingUpdate.maxSurge`               | Refer to [doc](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#deploymentstrategy-v1-apps) | 1                           |
| `wso2.deployment.mi.strategy.rollingUpdate.maxUnavailable`         | Refer to [doc](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#deploymentstrategy-v1-apps) | 0                           |
| `wso2.deployment.mi.livenessProbe.initialDelaySeconds`             | Initial delay for the live-ness probe for micro-integrator node                               | 40                           |
| `wso2.deployment.mi.livenessProbe.periodSeconds`                   | Period of the live-ness probe for micro-integrator node                                       | 10                           |
| `wso2.deployment.mi.readinessProbe.initialDelaySeconds`            | Initial delay for the readiness probe for micro-integrator node                               | 40                           |
| `wso2.deployment.mi.readinessProbe.periodSeconds`                  | Period of the readiness probe for micro-integrator node                                       | 10                           |
| `wso2.deployment.mi.imagePullPolicy`                               | Refer to [doc](https://kubernetes.io/docs/concepts/containers/images#updating-images)     | Always                       |
| `wso2.deployment.mi.resources.requests.memory`                     | The minimum amount of memory that should be allocated for a Pod                           | 1Gi                          |
| `wso2.deployment.mi.resources.requests.cpu`                        | The minimum amount of CPU that should be allocated for a Pod                              | 2000m                        |
| `wso2.deployment.mi.resources.limits.memory`                       | The maximum amount of memory that should be allocated for a Pod                           | 2Gi                          |
| `wso2.deployment.mi.resources.limits.cpu`                          | The maximum amount of CPU that should be allocated for a Pod                              | 2000m                        |
| `wso2.deployment.mi.envs                `                          | The List of environment variables that should configured for a Pod                              | 2000m                        |
| `wso2.deployment.mi.synapseTest.enabled                `           | Enable Synapse testing                                                                 | false                        |
| `wso2.ingress.services.hostname`                                   | The Host name for the Micro integrator Services ingress                                   | mi.wso2.com                        |
| `wso2.ingress.services.annotations`                                | Annotations for the Micro Integrator Services Ingress                                     | Nginx ingress annotations                      |
| `wso2.ingress.management.hostnname`                                | The Host name for the Micro integrator Management ingress                                 | management.mi.wso2.com                       |
| `wso2.ingress.management.annotations`                              | Annotations for the Micro Integrator Management Ingress                                   | Nginx ingress annotations                      |

##### 3. Deploy WSO2 Micro Integrator.

- **Helm v2**

    ```
    helm install --name <RELEASE_NAME> <HELM_HOME> --namespace <NAMESPACE>
    ```

- **Helm v3**
 
    ```
    helm install <RELEASE_NAME> <HELM_HOME> --namespace <NAMESPACE>
    ```

`NAMESPACE` should be the Kubernetes Namespace in which the resources are deployed

##### 4. Access Micro Integrator.

Default deployment will expose `<RELEASE_NAME>` as the host.

To access the Micro Integrator in the environment,

a. Obtain the external IP (`EXTERNAL-IP`) of the Ingress resources by listing down the Kubernetes Ingresses.

```
kubectl get ing -n <NAMESPACE>
```

```
NAME                                                  HOSTS                      ADDRESS        PORTS     AGE
<RELEASE_NAME>-micro-integrator-service-ingress       mi.wso2.com                <EXTERNAL-IP>  80, 443   3m
<RELEASE_NAME>-micro-integrator-management-ingress    management.mi.wso2.com     <EXTERNAL-IP>  80, 443   3m
```

b. Add the above host as an entry in /etc/hosts file as follows:

```
<EXTERNAL-IP>	mi.wso2.com 
<EXTERNAL-IP>	management.mi.wso2.com 
```
