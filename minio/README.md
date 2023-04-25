# Min Io Storage


### Step 1: Set up env

```sh
kubectl apply -f minio-standalone-namespace.yaml
kubectl apply -f minio-standalone-pvc.yaml
kubectl apply -f minio-standalone-deployment.yaml
kubectl apply -f minio-standalone-service.yaml
kubectl apply -f minio-standalone-ingress.yaml



# kubectl describe pods -n minio-dev
# kubectl logs pod/minio -n minio-dev
```



---

## Sources:

<!-- - [thanos prometheus sample project](https://github.com/AvnerZini/thanos_prometheus_project) -->

