# Create Cluster


```sh
gcloud container clusters create-auto autopilot-istio-example \
    --region=$REGION \
    --release-channel=regular \
    --cluster-version=1.27.2-gke.1200 \
    --workload-policies=allow-net-admin \
    --project=$PROJECT_ID
```



---

## Sources:

- [argocd install](https://chimbu.medium.com/installing-istio-not-anthos-service-mesh-on-gke-autopilot-2b78f1bbe90a)

