# learnk3d
Learn to set up moniter system by k3d

## Set up ENV

```
brew install k3d

k3d cluster create my-monitor-cluster \
  --agents 1 \
  --k3s-arg "--disable=traefik@server:0" \
  --port "8080:8080@loadbalancer" \
  --port "3000:3000@loadbalancer" \
  --wait

kubectl config get-contexts

```

## Helm
```
brew install helm

helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update

```

## Grafna and Prometheus-stack
```
kubectl create namespace monitoring
```

```
helm install kube-prometheus-stack prometheus-community/kube-prometheus-stack \
  --namespace monitoring \
  --create-namespace

kubectl --namespace monitoring get pods -l "release=kube-prometheus-stack"

# Grafana password
kubectl --namespae monitoring get secrets kube-prometheus-stack-grafana -o jsonpath="{.data.admin-password}" | base64 -d ; echo

export POD_NAME=$(kubectl --namespace monitoring get pod -l "app.kubernetes.io/name=grafana,app.kubernetes.io/instance=kube-prometheus-stack" -oname)
kubectl --namespace monitoring port-forward $POD_NAME 3000

```

## Loki and Promtail
```
helm repo add grafana https://grafana.github.io/helm-charts
helm repo update
```

### fail
```
helm install loki grafana/loki --namespace monitoring -f ./loki-helm.yaml 
helm install promtail grafana/promtail --namespace monitoring
```

### Trun off
```
helm -n monitoring list 

helm -n monitoring uninstall prometheus-community/kube-prometheus-stack
```
```
k3d cluster list

k3d cluster stop my-monitor-cluster
```
