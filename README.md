# istio-flux-tenant
Repository to handle istio manifest

## Istio

1. Envs
```bash
export ISTIO_PROFILE=default # default
# export ISTIO_PROFILE=demo   # demo
```

1. Download
```bash
curl -L https://istio.io/downloadIstio | sh -
cd istio-1.11.2
```

2. Instalation
```bash
istioctl install --set profile=${ISTIO_PROFILE} -y --context dev
```

```bash
export KUBE_CONTEXT=do-lon1-databackplaneuat
export KUBE_NAMESPACE=databackplane1
kubectl create namespace ${KUBE_NAMESPACE}
kubectl config use-context ${KUBE_CONTEXT}
kubectl config set-context --current --namespace=${KUBE_NAMESPACE}
kubectl config get-contexts
kubectl label namespace ${KUBE_NAMESPACE} istio-injection=enabled
kubectl label namespace ${KUBE_NAMESPACE} istio-injection=disable --overwrite
```

### Added kiali
```bash
kubectl apply -f samples/addons
kubectl rollout status deployment/kiali -n istio-system
istioctl dashboard kiali
```

kubectl apply -f istio/confluent-gateway.yaml

## Helm istio installation

[Istio Helm](https://istio.io/latest/docs/setup/install/helm/)

Configure helm repository
```bash
helm repo add istio https://istio-release.storage.googleapis.com/charts
helm repo update
```

Create namespace
```bash
kubectl create namespace istio-system
```

Install istio base
```bash
# Chart https://istio-release.storage.googleapis.com/charts/base-1.12.1.tgz
helm install istio-base istio/base -n istio-system
```

Install istiod
```bash
# Chart https://istio-release.storage.googleapis.com/charts/istiod-1.12.1.tgz
helm install istiod istio/istiod -n istio-system -f istiod-helm-values.yaml --wait
```

```bash
kubectl create namespace istio-ingress
kubectl label namespace istio-ingress istio-injection=enabled
# Chart https://istio-release.storage.googleapis.com/charts/gateway-1.12.1.tgz
helm install istio-ingress istio/gateway -n istio-ingress -f istioingress-helm-values.yaml --wait
```
