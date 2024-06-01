# kube-pires

![Type: application](https://img.shields.io/badge/Type-application-informational?style=flat-square)  ![Version: 2.0.0](https://img.shields.io/badge/Version-2.0.0-informational?style=flat-square)

Helm chart of example for deploy kube-pires

# Prerequisites

* Install the kubectl, helm and helm-docs commands following the instructions of the file [REQUIREMENTS.md](../REQUIREMENTS.md).

# Installing the Chart

* Access a Kubernetes cluster.

* Change the values according to the need of the environment in ``kube-pires/values.yaml`` file. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

* Test the installation with command:

```bash
helm upgrade --install kube-pires -f kube-pires/values.yaml kube-pires/ -n kube-pires --create-namespace --dry-run
```

* To install/upgrade the chart with the release name `kube-pires`:

```bash
helm upgrade --install kube-pires -f kube-pires/values.yaml kube-pires/ -n kube-pires --create-namespace
```

Create a port-forward with the follow command:

```bash
kubectl port-forward svc/kube-pires 3000:80 -n kube-pires
```

Open the browser and access the URL: http://localhost:3000

## Uninstalling the Chart

To uninstall/delete the `kube-pires` deployment:

```bash
helm uninstall kube-pires -n kube-pires
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

The following tables lists the configurable parameters of the chart and their default values.

Change the values according to the need of the environment in ``kube-pires/values.yaml`` file.

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Affinity configurations |
| autoscaling | object | `{"enabled":false,"maxReplicas":20,"minReplicas":1,"targetCPUUtilizationPercentage":80}` | Auto scaling configurations |
| fullnameOverride | string | `""` |  |
| image.pullPolicy | string | `"IfNotPresent"` | Pull policy of Docker image |
| image.repository | string | `"aeciopires/kube-pires"` | Docker image name |
| image.tag | string | `"1.0.0"` | Docker image tag |
| imagePullSecrets | list | `[]` | List of dockerconfig secrets names to use when pulling images |
| ingress.annotations | object | `{"alb.ingress.kubernetes.io/certificate-arn":"arn:aws:acm:AWS_REGION:AWS_ACCOUNT:certificate/ID_ACM_CERTIFICATE","alb.ingress.kubernetes.io/healthcheck-path":"/","alb.ingress.kubernetes.io/healthcheck-port":"http","alb.ingress.kubernetes.io/listen-ports":"[{\"HTTP\": 80}, {\"HTTPS\": 443}]","alb.ingress.kubernetes.io/scheme":"internet-facing","alb.ingress.kubernetes.io/ssl-redirect":"443","alb.ingress.kubernetes.io/tags":"Scost=kube-pires, Environment=testing, Terraform=true, Kubernetes=true","kubernetes.io/ingress.class":"alb"}` | Ingress annotations |
| ingress.className | string | `""` |  |
| ingress.enabled | bool | `false` | Enables Ingress |
| ingress.hosts | list | `[{"host":"kube-pires.aeciopires.com","paths":[{"path":"/","pathType":"Prefix"}]}]` | Ingress hosts |
| ingress.tls | list | `[]` | Ingress TLS configuration |
| karpenter.amiFamily | string | `"Bottlerocket"` | AMIFamily is a required field, dictating both the default bootstrapping logic for nodes provisioned  through this EC2NodeClass but also selecting a group of recommended, latest AMIs by default. Currently, Karpenter supports amiFamily values AL2, Bottlerocket, Ubuntu, Windows2019, Windows2022 and Custom. GPUs are only supported by default with AL2 and Bottlerocket. The AL2 amiFamily does not support ARM64 GPU instance type |
| karpenter.clusterName | string | `"CHANGE_HERE"` | Name of cluster. Change the term CHANGE_HERE by EKS cluster name if you want to use Karpenter. Example: testing-my-cluster |
| karpenter.disruption | object | `{"consolidateAfter":"30s","consolidationPolicy":"WhenEmpty","expireAfter":"720h"}` | Disruption section which describes the ways in which Karpenter can disrupt and replace Nodes. Configuration in this section constrains how aggressive Karpenter can be with performing operations like rolling Nodes due to them hitting their maximum lifetime (expiry) or scaling down nodes to reduce cluster cost |
| karpenter.disruption.consolidateAfter | string | `"30s"` | The amount of time Karpenter should wait after discovering a consolidation decision This value can currently only be set when the consolidationPolicy is 'WhenEmpty' You can choose to disable consolidation entirely by setting the string value 'Never' here |
| karpenter.disruption.consolidationPolicy | string | `"WhenEmpty"` | Describes which types of Nodes Karpenter should consider for consolidation. If using 'WhenUnderutilized', Karpenter will consider all nodes for consolidation and attempt to remove or replace Nodes when it discovers that the Node is underutilized and could be changed to reduce cost If using `WhenEmpty`, Karpenter will only consider nodes for consolidation that contain no workload pods |
| karpenter.disruption.expireAfter | string | `"720h"` | The amount of time a Node can live on the cluster before being removed Avoiding long-running Nodes helps to reduce security vulnerabilities as well as to reduce the chance of issues that can plague Nodes with long uptimes such as file fragmentation or memory leaks from system processes You can choose to disable expiration entirely by setting the string value 'Never' here |
| karpenter.enabled | bool | `false` | Enables support provisioner of Karpenter. Reference: https://karpenter.sh/. Tested only using EKS cluster 1.25 in AWS with Karpenter 0.33.0. |
| karpenter.instanceProfile | object | `{"name":"CHANGE_HERE","use":false}` | Name of instanceProfile EKS cluster. Conflicts with karpenter.role. Must specify one of "role" or "instanceProfile" for Karpenter to launch nodes Example: Karpenter-testing-my-cluster-2023120112554517810000001e |
| karpenter.labels | object | `{"app":"kube-pires","karpenter":"true"}` | Labels are arbitrary key-values that are applied to all nodes |
| karpenter.limits | object | `{"cpu":"2","memory":"8Gi"}` | Resource limits constrain the total size of the cluster. Limits prevent Karpenter from creating new instances once the limit is exceeded. |
| karpenter.metadataOptions | object | `{"httpEndpoint":"enabled","httpProtocolIPv6":"disabled","httpPutResponseHopLimit":2,"httpTokens":"required"}` | Optional, configures IMDS for the instance |
| karpenter.requirements | list | `[{"key":"karpenter.k8s.aws/instance-category","operator":"In","values":["c","m","r"]},{"key":"karpenter.k8s.aws/instance-cpu","operator":"In","values":["2","4","8","16","32"]},{"key":"kubernetes.io/arch","operator":"In","values":["amd64"]},{"key":"kubernetes.io/os","operator":"In","values":["linux"]},{"key":"karpenter.sh/capacity-type","operator":"In","values":["spot","on-demand"]}]` | Requirements that constrain the parameters of provisioned nodes. These requirements are combined with pod.spec.topologySpreadConstraints, pod.spec.affinity.nodeAffinity, pod.spec.affinity.podAffinity, and pod.spec.nodeSelector rules. Operators { In, NotIn, Exists, DoesNotExist, Gt, and Lt } are supported. https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#operators |
| karpenter.resourceTags | object | `{"Environment":"testing","Scost":"kube-pires","customerid":"p1t2r3e4g5","product":"kube-pires","shardid":"sid1"}` | Karpenter adds tags to all resources it creates, including EC2 Instances, EBS volumes, and Launch Templates. See details: https://karpenter.sh/v0.33/concepts/nodeclasses/#spectags |
| karpenter.role | object | `{"name":"CHANGE_HERE","use":true}` | Name of role EKS cluster. The Karpenter spec.instanceProfile field has been removed from the EC2NodeClass in favor of the spec.role field. Karpenter is also removing support for the defaultInstanceProfile specified globally in the karpenter-global-settings, making the spec.role field required for all EC2NodeClasses. Karpenter will now auto-generate the instance profile in your EC2NodeClass, given the role that you specify. If using the Karpenter Getting Started Guide to deploy Karpenter, you can use the karpenter-irsa-$CLUSTER_NAME-$ID role  provisioned by that process (which is limited to 64 characters). Example: karpenter-irsa-testing-my-cluster-2023120421433226760000001e |
| karpenter.tag | string | `"karpenter.sh/discovery"` | Tag of discovery with name of cluster used by Karpenter. Change the term CHANGE_HERE by EKS cluster name if you want to use Karpenter. The cluster name, security group and subnets must have this tag. |
| karpenter.weight | int | `10` | Priority given to the NodePool when the scheduler considers which NodePool to select. Higher weights indicate higher priority when comparing NodePools. Specifying no weight is equivalent to specifying a weight of 0. |
| livenessProbe | object | `{"failureThreshold":3,"initialDelaySeconds":5,"path":"/health","periodSeconds":10,"successThreshold":1,"timeoutSeconds":5}` | Healh check continuos |
| livenessProbe.failureThreshold | int | `3` | When a probe fails, Kubernetes will try failureThreshold times before giving up. Giving up in case of liveness probe means restarting the container. In case of readiness probe the Pod will be marked Unready |
| livenessProbe.initialDelaySeconds | int | `5` | Number of seconds after the container has started before liveness |
| livenessProbe.path | string | `"/health"` | Path of health check of application |
| livenessProbe.periodSeconds | int | `10` | Specifies that the kubelet should perform a liveness probe every N seconds |
| livenessProbe.successThreshold | int | `1` | Minimum consecutive successes for the probe to be considered successful after having failed |
| livenessProbe.timeoutSeconds | int | `5` | Number of seconds after which the probe times out |
| mySecret | object | `{"enabled":true,"mySecretDistributedTracingEnabled":"true","mySecretHighSecurity":"false","mySecretLicenseKey":"mysecurepassword2"}` | Configurations of the application. Create configMap and Secret for use in deployment as environment variable |
| nameOverride | string | `""` |  |
| nodeSelector | object | `{}` | Node selector configurations |
| pdb.enabled | bool | `false` |  |
| pdb.maxUnavailable | string | `"25%"` |  |
| podAnnotations | object | `{}` | Pod annotations configurations |
| podSecurityContext | object | `{}` | Pod security configurations |
| readinessProbe | object | `{"failureThreshold":3,"initialDelaySeconds":5,"path":"/health","periodSeconds":10,"successThreshold":1,"timeoutSeconds":5}` | Health check on creation pod |
| readinessProbe.failureThreshold | int | `3` | When a probe fails, Kubernetes will try failureThreshold times before giving up. Giving up in case of liveness probe means restarting the container. In case of readiness probe the Pod will be marked Unready |
| readinessProbe.initialDelaySeconds | int | `5` | Number of seconds after the container has started before readiness |
| readinessProbe.path | string | `"/health"` | Path of health check of application |
| readinessProbe.periodSeconds | int | `10` | Specifies that the kubelet should perform a liveness probe every N seconds |
| readinessProbe.successThreshold | int | `1` | Minimum consecutive successes for the probe to be considered successful after having failed |
| readinessProbe.timeoutSeconds | int | `5` | Number of seconds after which the probe times out |
| replicaCount | int | `2` | Number of replicas. Used if autoscaling is false |
| resources | object | `{"limits":{"cpu":"200m","memory":"256Mi"},"requests":{"cpu":"5m","memory":"5Mi"}}` | Requests and limits of pod resources. See: [https://kubernetes.io/docs/concepts/configuration/manage-resources-containers](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers) |
| securityContext | object | `{}` | Security context configurations |
| service.port | int | `80` | Port of service in Kubernetes cluster |
| service.type | string | `"NodePort"` | Type of service in Kubernetes cluster |
| serviceMonitor | object | `{"additionalLabels":{},"enabled":false,"interval":"30s","namespace":"kube-pires","namespaceSelector":{},"path":"/metrics","scrapeTimeout":"10s"}` | Service monitor configurations |
| tolerations | list | `[]` | Tolerations configurations |
| updateStrategy | object | `{"rollingUpdate":{"maxSurge":6,"maxUnavailable":0},"type":"RollingUpdate"}` | Update strategy configurations |
