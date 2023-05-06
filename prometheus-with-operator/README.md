# Monitoring stack with Opearator

This is moniroting stack for k8s applications.



### Step 1: Install Opearator

Install prometheus-opeartor

```sh
LATEST=$(curl -s https://api.github.com/repos/prometheus-operator/prometheus-operator/releases/latest | jq -cr .tag_name)
curl -sL https://github.com/prometheus-operator/prometheus-operator/releases/download/${LATEST}/bundle.yaml | kubectl create -f -
```

You can check for completion with the following command

```sh
kubectl wait --for=condition=Ready pods -l  app.kubernetes.io/name=prometheus-operator -n default
```

### Step 2: Create ClusterRole for Prometheus

Create a storage class to enable Prometheus to persist date:
```sh
kubectl create -f prometheus-cluster-role.yaml
kubectl create -f prometheus-cluster-role-binding.yaml
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

