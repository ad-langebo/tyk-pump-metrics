# tyk-pump missing metrics issue

## repro steps
1. create kind cluster via `kind create cluster --config kind-cluster.yaml` (optional)
2. install redis via bitnami helm chart `helm install tyk-redis oci://registry-1.docker.io/bitnamicharts/redis -f redis-values.yaml`
3. apply httpbin deployment, service and configmap `kubectl apply -f httpbin.yaml`
4. add helm repo `helm repo add tyk-helm https://helm.tyk.io/public/helm/charts/`
5. install tyk-oss global chart `helm install tyk-oss tyk-helm/tyk-oss --version 2.2.0 -f tyk-values.yaml`

