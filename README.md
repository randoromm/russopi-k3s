# russopi-k3s
Manifest files for raspberry pi k3s cluster

## To deploy (in specific directory):
```
kubectl kustomize | kubectl apply -f -
```

## Delete/undeploy:
```
kubectl kustomize | kubectl delete -f -
```
