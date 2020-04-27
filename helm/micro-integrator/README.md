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
[Option 1] Deploy using Docker images from DockerHub.

```
helm install --name <RELEASE_NAME> wso2/micro-integrator --version 1.2.0-1 --namespace <NAMESPACE>
```

[Option 2] Deploy WSO2 Micro Integrator  using Docker images from WSO2 Private Docker Registry.
```
helm install --name <RELEASE_NAME> wso2/micro-integrator --version 1.2.0-1 --namespace <NAMESPACE> --set wso2.subscription.username=<SUBSCRIPTION_USERNAME> --set wso2.subscription.password=<SUBSCRIPTION_PASSWORD>
```
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
NAME                                     HOSTS                ADDRESS        PORTS     AGE
<RELEASE_NAME>-wso2micro-integrator      <RELEASE_NAME>       <EXTERNAL-IP>  80, 443   3m
```

b. Add the above host as an entry in /etc/hosts file as follows:

```
<EXTERNAL-IP>	<RELEASE_NAME>
```
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

###### Centralized Logging Configurations

| Parameter                                                                   | Description                                                                               | Default Value               |
|-----------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------|
| `wso2.centralizedLogging.enabled`                                           | Enable Centralized logging for WSO2 components                                            | true                        |                                                                                         |                             |    
| `wso2.centralizedLogging.logstash.imageTag`                                 | Logstash Sidecar container image tag                                                      | 7.2.0                       |  
| `wso2.centralizedLogging.logstash.elasticsearch.host    `                   | Elasticsearch endpoint                                                                    | elastic                     |  
| `wso2.centralizedLogging.logstash.elasticsearch.username`                   | Elasticsearch username                                                                    | elastic                     |  
| `wso2.centralizedLogging.logstash.elasticsearch.password`                   | Elasticsearch password                                                                    | changeme                    |  

###### Micro Integrator Deployment Configurations

| Parameter                                                                   | Description                                                                               | Default Value               |
|-----------------------------------------------------------------------------|-------------------------------------------------------------------------------------------|-----------------------------|
| `wso2.deployment.wso2microIntegrator.dockerRegistry`                                | Docker registry of the micro-integrator image                                                 | ""                          |
| `wso2.deployment.wso2microIntegrator.imageName`                                     | Image name for micro-integrator node                                                          | ""                          |
| `wso2.deployment.wso2microIntegrator.imageTag`                                      | Image tag for micro-integrator node                                                           | ""                          |
| `wso2.deployment.wso2microIntegrator.replicas`                                      | Number of replicas for micro-integrator node                                                  | 1                           |
| `wso2.deployment.wso2microIntegrator.minReadySeconds`                               | Refer to [doc](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#deploymentspec-v1-apps)| 1  75                        |
| `wso2.deployment.wso2microIntegrator.strategy.rollingUpdate.maxSurge`               | Refer to [doc](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#deploymentstrategy-v1-apps) | 1                           |
| `wso2.deployment.wso2microIntegrator.strategy.rollingUpdate.maxUnavailable`         | Refer to [doc](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.10/#deploymentstrategy-v1-apps) | 0                           |
| `wso2.deployment.wso2microIntegrator.livenessProbe.initialDelaySeconds`             | Initial delay for the live-ness probe for micro-integrator node                               | 40                           |
| `wso2.deployment.wso2microIntegrator.livenessProbe.periodSeconds`                   | Period of the live-ness probe for micro-integrator node                                       | 10                           |
| `wso2.deployment.wso2microIntegrator.readinessProbe.initialDelaySeconds`            | Initial delay for the readiness probe for micro-integrator node                               | 40                           |
| `wso2.deployment.wso2microIntegrator.readinessProbe.periodSeconds`                  | Period of the readiness probe for micro-integrator node                                       | 10                           |
| `wso2.deployment.wso2microIntegrator.imagePullPolicy`                               | Refer to [doc](https://kubernetes.io/docs/concepts/containers/images#updating-images)     | Always                       |
| `wso2.deployment.wso2microIntegrator.resources.requests.memory`                     | The minimum amount of memory that should be allocated for a Pod                           | 1Gi                          |
| `wso2.deployment.wso2microIntegrator.resources.requests.cpu`                        | The minimum amount of CPU that should be allocated for a Pod                              | 2000m                        |
| `wso2.deployment.wso2microIntegrator.resources.limits.memory`                       | The maximum amount of memory that should be allocated for a Pod                           | 2Gi                          |
| `wso2.deployment.wso2microIntegrator.resources.limits.cpu`                          | The maximum amount of CPU that should be allocated for a Pod                              | 2000m                        |
| `wso2.deployment.wso2microIntegrator.envs                `                          | The List of environment variables that should configured for a Pod                              | 2000m                        |
| `wso2.ingress.host`                                                                 | The Host name for the Micro integrator ingress                                            | Release name                        |
| `wso2.ingress.annotations`                                                          | Annotations for the Micro Integrator Ingress                                              | Nginx ingress annotations                      |

##### 3. Deploy WSO2 Micro Integrator.

```
helm install --name <RELEASE_NAME> <HELM_HOME> --namespace <NAMESPACE>
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
NAME                                     HOSTS                ADDRESS        PORTS     AGE
<RELEASE_NAME>-wso2micro-integrator      <RELEASE_NAME>       <EXTERNAL-IP>  80, 443   3m
```

b. Add the above host as an entry in /etc/hosts file as follows:

```
<EXTERNAL-IP>	<RELEASE_NAME>
```

## Enabling Centralized Logging

Centralized logging with Logstash and Elasticsearch is disabled by default. However, if it is required to be enabled, 
the following steps should be followed.

1. Set `centralizedLogging.enabled` to `true` in the [values.yaml](values.yaml) file.
2. Add elasticsearch Helm repository to download sub-charts required for Centralized logging.
```
helm repo add elasticsearch https://helm.elastic.co
```
3. Create a requirements.yaml at <HELM_HOME> and add the following dependencies in the file.
```
dependencies:
  - name: kibana
    version: "7.2.1-0"
    repository: "https://helm.elastic.co"
    condition: wso2.centralizedLogging.enabled
  - name: elasticsearch
    version: "7.2.1-0"
    repository: "https://helm.elastic.co"
    condition: wso2.centralizedLogging.enabled

```
4. Add override configurations for Elasticsearch in the [values.yaml](values.yaml) file.
```
wso2:
  ( ... )
  
elasticsearch:
  clusterName: wso2-elasticsearch
```
