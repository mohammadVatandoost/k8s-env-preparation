# EFK


### Step 1: Set up env

```sh
kubectl create -f kube-logging.yaml
```

Install elasticsearch, we’ve set up our headless service and a stable .elasticsearch.kube-logging.svc.cluster.local domain for our Pods
```sh
kubectl create -f elasticsearch_svc.yaml
kubectl create -f elasticsearch_statefulset.yaml
```

You can monitor the StatefulSet as it is rolled out using kubectl rollout status
```sh
kubectl rollout status sts/es-cluster --namespace=kube-logging
```


To do so, first forward the local port 9200 to the port 9200 on one of the Elasticsearch nodes (es-cluster-0) using kubectl port-forward:
```sh
kubectl port-forward es-cluster-0 9200:9200 --namespace=kube-logging
curl http://localhost:9200/_cluster/state?pretty
```

Create kibana
```sh
kubectl create -f kibana.yaml
kubectl rollout status deployment/kibana --namespace=kube-logging
kubectl port-forward kibana-6c9fb4b5b7-plbg2 5601:5601 --namespace=kube-logging
```

see http://localhost:5601

Create fluentd deamonset
```sh
kubectl create -f fluentd.yaml
```

Testing Container Logging
```sh
kubectl create -f counter.yaml
```

---

## Sources:

- [How To Set Up an Elasticsearch, Fluentd and Kibana (EFK) Logging Stack on Kubernetes](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-elasticsearch-fluentd-and-kibana-efk-logging-stack-on-kubernetes)

