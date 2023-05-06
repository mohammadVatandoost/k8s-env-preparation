# Loki


### Step 1: Set up env

```sh
helm repo add grafana https://grafana.github.io/helm-charts
helm install loki grafana/loki-stack --namespace loki --create-namespace --set grafana.enabled=true
```

Username: admin
Password: the password you extracted at the previous step...
```sh
kubectl get secret --namespace loki loki-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

Port forward
```sh
kubectl port-forward --namespace loki service/loki-grafana 3000:80
```

Install Ingress
```sh
kubectl apply -f ingress.yaml --namespace loki 
```

---

## Sources:

<!-- - [thanos prometheus sample project](https://github.com/AvnerZini/thanos_prometheus_project) -->

