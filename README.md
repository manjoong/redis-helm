# Redis-Cluster

Redis-Cluster is an advanced key-value cache and store. It is composed of slaves and master, ensuring stability and efficiency.

## TL;DR

```bash
# Testing configuration
$ helm install local/redis-cluster
```


## Introduction

It ensures efficiency by placing multiple slaves on one master, and splits and stores information on several masters, there by improving efficiency.

## Prerequisites

- Kubernetes 1.8+
- PV provisioner support in the underlying infrastructure

## Installing the Chart

To install the chart with the release name `my-release`:

```bash
$ helm install --name my-release local/redis-cluster
```

The command deploys Redis on the Kubernetes cluster in the default configuration. The [configuration](#configuration) section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```bash
$ helm delete my-release
```
The command removes all the Kubernetes components associated with the chart and deletes the release.

## Configuration

The following table lists the configurable parameters of the Redis cluster and their default values.

| Parameter                                     | Description                                                                                                                                         | Default                                                 |
|-----------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------|
| `replicaCount`                 | Count of Master or Slave Pods     | `6`                  |
| `fullnameOverride`                     | Name of all resources          | `redis-cluster` |
| `redisService.type`             | Redis Image Service Type              | `NodePort`      |
| `redisService.port`             | Redis Image Service Port              | `6379`          |
| `exporterService.port`               | Redis Exporter Service Port      | `16379`         |
| `storageClassName`               | Dynamic Provisioning Storage Class Name  | `nfs-client` |
| `securityContext.enabled`        | Confirm that want to use securityContext  | `false` |
| `securityContext.fsGroup`      | Security group user id                      | `1001` |
| `securityContext.runAsUser`    | Security Running User id               | `1001`   |
| `nodeSelector`               | Redis Node labels for pod assignment      | `nil`    |
| `priorityClassName`          | Metrics exporter pod priorityClassName    | `{}` |
| `tolerations`         | Toleration labels for Redis pod assignment         | `nil`     |
| `resources`            | resource requests/limit                        | `{}`  |



Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```bash
$ helm install --name my-release \
  --set replicaCount={{ number }} \
    local/redis-cluster
```

The above command sets the Redis Pod's COunt to `replicaCount`.


> **Tip**: You can use the default [values.yaml](values.yaml)

> **Note for minikube users**: Current versions of minikube (v0.24.1 at the time of writing) provision `hostPath` persistent volumes that are only writable by root. Using chart defaults cause pod failure for the Redis pod as it attempts to write to the `/bitnami` directory. Consider installing Redis with `--set persistence.enabled=false`. See minikube issue [1990](https://github.com/kubernetes/minikube/issues/1990) for more information.



### Existing Dynamic Volume Provisioner(DVP)

1. Create the DVP
2. Install the chart

```bash
$ helm install --set storageClassName=DVP_NAME local/redis-cluster
```

### How to Set Cluster

1. Create the DVP
2. Install the chart
3. Type this command

```bash
$ kubectl exec -it -c redis -n redis-cluster redis-cluster-0 -- redis-cli --cluster create --cluster-replicas 1 \
$(kubectl get pods -n redis-cluster -l app=redis-cluster -o jsonpath='{range.items[*]}{.status.podIP}:6379 ')
```
4. Check your Cluster
```bash
kubectl exec -it -c redis -n redis-cluster redis-cluster-0 -- redis-cli cluster nodes
