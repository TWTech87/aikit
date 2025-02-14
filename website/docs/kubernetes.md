---
title: Kubernetes Deployment
---

It is easy to get started to deploy your models to Kubernetes! You can deploy your models with the [Helm chart](#helm-chart) or [manually](#manual-deployment).

Make sure you have a Kubernetes cluster running and `kubectl` is configured to talk to it, and your model images are accessible from the cluster.

:::tip
You can use [kind](https://kind.sigs.k8s.io/) to create a local Kubernetes cluster for testing purposes.
:::

## Helm Chart

For advanced deployments or customization options, you can use the [Helm chart](https://helm.sh/) provided in the `charts` directory.

Please make sure you have Helm installed and configured. If you don't have Helm installed, you can follow the instructions [here](https://helm.sh/docs/intro/install/).

Install the chart using the following command:

```bash
helm install aikit ./charts/aikit
```

Output will be similar to:

```bash
NAME: aikit
LAST DEPLOYED: Wed May 15 05:32:39 2024
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
Get the application URL by running these commands:
  echo "Visit http://127.0.0.1:8080 to access aikit WebUI."
  kubectl --namespace default port-forward service/aikit-webui 8080:80
```

As mentioned in the notes, you can then port-forward and send requests to your model, or navigate to the URL provided to access the WebUI.

```bash
# port-forward for testing locally
kubectl port-forward service/aikit 8080:8080 &

 # send requests to your model
curl http://localhost:8080/v1/chat/completions -H "Content-Type: application/json" -d '{
    "model": "llama-3-8b-instruct",
    "messages": [{"role": "user", "content": "explain kubernetes in a sentence"}]
  }'
{"created":1716695271,"object":"chat.completion","id":"809d031e-d78a-4e3a-9719-04683d9e29f9","model":"llama-3-8b-instruct","choices":[{"index":0,"finish_reason":"stop","message":{"role":"assistant","content":"Kubernetes is an open-source container orchestration system that automates the deployment, scaling, and management of applications and services in a cloud-native environment."}}],"usage":{"prompt_tokens":11,"completion_tokens":31,"total_tokens":42}}
```

### Values

| Key                                          | Type    | Default                                                                               | Description                                        |
| -------------------------------------------- | ------- | ------------------------------------------------------------------------------------- | -------------------------------------------------- |
| `image.repository`                           | String  | `ghcr.io/sozercan/llama3`                                                             | The image repository                               |
| `image.tag`                                  | String  | `8b`                                                                                  | The image tag                                      |
| `image.pullPolicy`                           | String  | `IfNotPresent`                                                                        | The image pull policy                              |
| `replicaCount`                               | Integer | `1`                                                                                   | The number of replicas                             |
| `imagePullSecrets`                           | Array   | `[]`                                                                                  | Image pull secrets                                 |
| `nameOverride`                               | String  | `""`                                                                                  | Override the name                                  |
| `fullnameOverride`                           | String  | `""`                                                                                  | Override the fullname                              |
| `podAnnotations`                             | Object  | `{}`                                                                                  | Pod annotations                                    |
| `podLabels`                                  | Object  | `{}`                                                                                  | Pod labels                                         |
| `podSecurityContext`                         | Object  | `{}`                                                                                  | Pod security context                               |
| `securityContext`                            | Object  | `{}`                                                                                  | Security context                                   |
| `service.type`                               | String  | `ClusterIP`                                                                           | Service type                                       |
| `service.port`                               | Integer | `8080`                                                                                | Service port                                       |
| `resources.limits.cpu`                       | String  | `4`                                                                                   | CPU resource limits                                |
| `resources.limits.memory`                    | String  | `4Gi`                                                                                 | Memory resource limits                             |
| `resources.requests.cpu`                     | String  | `100m`                                                                                | CPU resource requests                              |
| `resources.requests.memory`                  | String  | `128Mi`                                                                               | Memory resource requests                           |
| `livenessProbe.httpGet.path`                 | String  | `/`                                                                                   | Path for the liveness probe                        |
| `livenessProbe.httpGet.port`                 | String  | `http`                                                                                | Port for the liveness probe                        |
| `readinessProbe.httpGet.path`                | String  | `/`                                                                                   | Path for the readiness probe                       |
| `readinessProbe.httpGet.port`                | String  | `http`                                                                                | Port for the readiness probe                       |
| `autoscaling.enabled`                        | Boolean | `false`                                                                               | If autoscaling is enabled                          |
| `autoscaling.minReplicas`                    | Integer | `1`                                                                                   | Minimum number of replicas for autoscaling         |
| `autoscaling.maxReplicas`                    | Integer | `100`                                                                                 | Maximum number of replicas for autoscaling         |
| `autoscaling.targetCPUUtilizationPercentage` | Integer | `80`                                                                                  | Target CPU utilization percentage for autoscaling  |
| `nodeSelector`                               | Object  | `{}`                                                                                  | Node selector                                      |
| `affinity`                                   | Object  | `{}`                                                                                  | Affinity settings                                  |
| `ui.enabled`                                 | Boolean | `true`                                                                                | If the UI is enabled                               |
| `ui.models`                                  | Array   | `[llama-3-8b-instruct]`                                                               | List of models                                     |
| `ui.defaultModel`                            | String  | `llama-3-8b-instruct`                                                                 | Default model                                      |
| `ui.replicaCount`                            | Integer | `1`                                                                                   | Number of UI replicas                              |
| `ui.type`                                    | String  | `lobe-chat`                                                                           | Type of UI                                         |
| `ui.image.repository`                        | String  | `lobehub/lobe-chat`                                                                   | The UI image repository                            |
| `ui.image.pullPolicy`                        | String  | `IfNotPresent`                                                                        | The UI image pull policy                           |
| `ui.image.tag`                               | String  | `v0.161.19`                                                                           | The UI image tag                                   |
| `ui.service.type`                            | String  | `ClusterIP`                                                                           | UI service type                                    |
| `ui.service.port`                            | Integer | `80`                                                                                  | UI service port                                    |
| `ui.nodeSelector`                            | Object  | `{}`                                                                                  | UI node selector                                   |
| `ui.tolerations`                             | Array   | `[]`                                                                                  | UI tolerations                                     |
| `ui.affinity`                                | Object  | `{}`                                                                                  | UI affinity settings                               |
| `ui.ingress.enabled`                         | Boolean | `false`                                                                               | If UI ingress is enabled                           |
| `ui.ingress.className`                       | String  | `""`                                                                                  | Ingress class name                                 |
| `ui.ingress.annotations`                     | Object  | `{}`                                                                                  | Ingress annotations                                |
| `ui.ingress.hosts`                           | Array   | `[{host: chart-example.local, paths: [{path: /, pathType: ImplementationSpecific}]}]` | Ingress hosts and paths                            |
| `ui.ingress.tls`                             | Array   | `[]`                                                                                  | Ingress TLS settings                               |
| `ui.persistentVolume.enabled`                | Boolean | `false`                                                                               | If Persistent Volume is enabled for UI             |
| `ui.persistentVolume.storageClass`           | String  | `default`                                                                             | Storage class for the Persistent Volume            |
| `ui.persistentVolume.accessModes`            | Array   | `["ReadWriteOnce"]`                                                                   | Access modes for the Persistent Volume             |
| `ui.persistentVolume.size`                   | String  | `10Gi`                                                                                | Size of the Persistent Volume                      |
| `ui.persistentVolume.claimName`              | String  | `""`                                                                                  | Claim name for an existing Persistent Volume Claim |


## Manual Deployment

You can also deploy your models manually using `kubectl`. Here is an example:

```bash
# create a deployment
# replace the image with your own if needed
kubectl create deployment aikit-llama3 --image=ghcr.io/sozercan/llama3:8b

# expose it as a service
kubectl expose deployment aikit-llama3 --port=8080 --target-port=8080 --name=aikit

# easy to scale up and down as needed
kubectl scale deployment aikit-llama3 --replicas=3

# port-forward for testing locally
kubectl port-forward service/aikit 8080:8080 &

# send requests to your model
curl http://localhost:8080/v1/chat/completions -H "Content-Type: application/json" -d '{
     "model": "llama-3-8b-instruct",
     "messages": [{"role": "user", "content": "explain kubernetes in a sentence"}]
   }'
{"created":1701236489,"object":"chat.completion","id":"dd1ff40b-31a7-4418-9e32-42151ab6875a","model":"llama-3-8b-instruct","choices":[{"index":0,"finish_reason":"stop","message":{"role":"assistant","content":"\nKubernetes is a container orchestration system that automates the deployment, scaling, and management of containerized applications in a microservices architecture."}}],"usage":{"prompt_tokens":0,"completion_tokens":0,"total_tokens":0}}
```
