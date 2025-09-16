# tyk-pump missing metrics issue

## prerequisities

- kind (or other k8s cluster access)
- helm

## repro steps

### setup infrastructure

1. create kind cluster via 

```bash 
kind create cluster --config kind-cluster.yaml
```

2. install redis via bitnami helm chart

```shell
helm install tyk-redis oci://registry-1.docker.io/bitnamicharts/redis -f redis-values.yaml
```

3. apply httpbin deployment and service 

```shell 
kubectl apply -f httpbin.yaml
```

4. apply tyk apps pvc 

```shell 
kubectl apply -f tyk-apps-pvc.yaml
```

5. add helm repo 

```shell 
helm repo add tyk-helm https://helm.tyk.io/public/helm/charts/
```

6. install tyk-oss global chart 

```shell 
helm install tyk-oss tyk-helm/tyk-oss --version 2.2.0 -f tyk-values.yaml
```

7. port-forward gateway, control and pump 

```shell
kubectl port-forward deploy/ ???
```

### register & invoke OAS api

8. register httpbin OAS api via 

```shell 
curl -L 'http://localhost:9696/tyk/apis/oas/import' -H 'Content-Type: application/json' -H 'X-Tyk-Authorization: foo' -d "@httpbin-oas.json"
```

9. reload tyk 

```shell 
curl -L 'http://localhost:9696/tyk/reload/group' -H 'X-Tyk-Authorization: foo'
```

10. hit httpbin endpoints via the gateway

```shell
curl -L 'http://localhost:8080/httpbin/uuid'
```

11. query pump metrics endpoint

```shell
curl -L 'http://localhost:9090/metrics'
```

this will not return any tyk_ specific metrics whatsoever

### register & invoke legacy api

12. register httpbin legacy api via

```shell
curl -L 'http://localhost:9696/tyk/apis' -H 'Content-Type: application/json' -H 'X-Tyk-Authorization: foo' -d "@httpbin-oas.json"
```

13. reload tyk again

```shell 
curl -L 'http://localhost:9696/tyk/reload/group' -H 'X-Tyk-Authorization: foo'
```

14. hit httpbin legacy endpoints via the gateway

```shell
curl -L 'http://localhost:8080/keyless-test/uuid'
```

15. query pump metrics endpoint again

```shell
curl -L 'http://localhost:9090/metrics'
```

this will result in tyk_ specific metrics being available for the legacy keyless-test api