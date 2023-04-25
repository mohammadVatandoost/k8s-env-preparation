# Monitoring

This is moniroting stack for k8s applications.

## Features

- Multi `golang` microservices in the mono repo
- Single build pipeline
- Shared `golang` library
- Shared `grpc/protobuf` library
- Automatic release GitHub workflow action
    - macOS and Linux builds


### Step 1: Set up env

Create a monitoring namespace

```sh
kubectl create namespace monitoring
```

create an RBAC policy

```sh
kubectl create -f clusterRole.yaml
```

Create a storage class to enable Prometheus to persist date:
```sh
kubectl apply -f prometheus-storage-class.yaml -n monitoring
```

Add Helm Repo:
```sh
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```

Create the secret for storage:
```sh
kubectl -n monitoring create secret generic thanos-objstore-config --from-file=thanos.yaml=thanos-storage-config.yaml
```




### Step 2: Deploy Prometheus and Thanos in a cluster

install/upgrade the helm chart with our relevant customizations.

```sh
helm install kube-prometheus -f values.yaml bitnami/kube-prometheus -n monitoring
helm upgrade kube-prometheus -f values.yaml bitnami/kube-prometheus -n monitoring
```

<!-- ```sh
kubectl create  -f prometheus-deployment.yaml 

``` -->

### Step 3: Configure Thanos on the main observability cluster. If you have one cluster, put it in the same cluster

As mentioned before, it will be responsible to collect all the data from all the clusters that we deployed in the first phase
For that, we use kube-thanos manifests. We found that for our purpose we need to implement only the query and the store parts.

```sh
kubectl create ns thanos
```

we’ll take care of communication between thanos-store to the S3 bucket (ObjectStore) that we configured data to be sent to from the first phase. So, as we did in the first step, we need to configure a secret with a name as requested in thanos-store-statefulSet.yamlpart of the injected environment to the Thanos store pods:

If you have one cluster, This step is done before, you don't need to run it.
```sh
kubectl -n thanos create secret generic thanos-objectstorage --from-file=thanos.yaml=thanos-storage-config.yaml
```

Install Thanos manifests:
```sh
kubectl apply -f manifests -n thanos
```

---

## Sources:

- [thanos prometheus sample project](https://github.com/AvnerZini/thanos_prometheus_project)

