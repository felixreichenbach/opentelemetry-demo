# Notes

## Deploy OTEL Demo Without Instrumentation

### Prerequisits

Default resource allocation for Docker limits memory to ~7GB.
Consider increasing it to at least 16GB.

### Confirm the target cluster first

```shell
kubectl config current-context
kubectl cluster-info
```

### Configure the Helm repository

```shell
helm repo add open-telemetry \
  https://open-telemetry.github.io/opentelemetry-helm-charts
helm repo update
```

### Build Modified `email` Container

```shell
build -f src/email/Dockerfile -t email:local .
```


### Install OTEL Demo Application

```shell
helm upgrade --install otel-demo \
  open-telemetry/opentelemetry-demo \
  --namespace otel-demo \
  --create-namespace \
  --values no-telemetry-values.yaml \
  --wait \
  --timeout 10m
```

### Access It

```
kubectl get pods -n otel-demo
helm status otel-demo -n otel-demo

kubectl port-forward -n otel-demo \
  svc/frontend-proxy 8080:8080
```

Open http://localhost:8080


### Cleanup

```
helm uninstall otel-demo -n otel-demo
kubectl delete namespace otel-demo
```

```shell
helm uninstall grafana-cloud -n monitoring
```
