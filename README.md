# Ethereum Helm Charts

Ethereum Helm charts for Kubernetes - select your choice of `elc` and `clc` client and you can deploy it to K8S. This also supports `aws` and `azure` so you can pick the appropriate provider. If you are running this locally, please make sure you have a lot of resources avialable ie. disk space, cpu and RAM

This currently **only** supports beacon nodes, not validators

## Values

| Key      | Type   | Default     | Description |
|----------|--------|-------------|-------------|
| provider | string | `"local"`   |             |
| network  | string | `"sepolia"` |             |
| env      | string | `"dev"`     |             |
| elc      | string | `"besu"`    |             |
| clc      | string | `"teku"`    |             |


### Requirements
1. You need `kube-prometheus-stack` or equivalent that installs the `ServiceMonitor` CRDs

```
helm install [RELEASE_NAME] oci://ghcr.io/prometheus-community/charts/kube-prometheus-stack
```

### Test the charts:
local
```
helm template jf ./charts/ethereum-helm-charts --namespace ethereum --create-namespace --values ./examples/mainnet/besu-teku-local.yaml > ./artifacts/besu-teku-mainnet-local.yml
```
aws
```
helm template jw ./charts/ethereum-helm-charts --namespace ethereum --create-namespace --values ./examples/mainnet/besu-teku-aws.yaml >./artifacts/besu-teku-mainnet-aws.yml
```
azure
```
helm template jfbt ./charts/ethereum-helm-charts --namespace ethereum --create-namespace --values ./examples/mainnet/besu-teku-azure.yaml > ./artifacts/besu-teku-mainnet-azure.yml

helm template jfgt ./charts/ethereum-helm-charts --namespace ethereum --create-namespace --values ./examples/mainnet/geth-teku-azure.yaml > ./artifacts/geth-teku-mainnet-azure.yml

helm template jfnt ./charts/ethereum-helm-charts --namespace ethereum --create-namespace --values ./examples/mainnet/nethermind-teku-azure.yaml > ./artifacts/nethermind-teku-mainnet-azure.yml

helm template jfbp ./charts/ethereum-helm-charts --namespace ethereum --create-namespace --values ./examples/mainnet/besu-prysm-azure.yaml > ./artifacts/besu-prysm-mainnet-azure.yml

helm template jfbld ./charts/ethereum-helm-charts --namespace ethereum --create-namespace --values ./examples/mainnet/besu-lodestar-azure.yaml > ./artifacts/besu-lodestar-mainnet-azure.yml

helm template jfbn ./charts/ethereum-helm-charts --namespace ethereum --create-namespace --values ./examples/mainnet/besu-nimbus-azure.yaml > ./artifacts/besu-nimbus-mainnet-azure.yml

KUBECONFIG=~/.kube/besu-azure kubectl -n ethereum apply -f ./artifacts/besu-lodestar-mainnet-azure.yml
```

## TODO
- support for validators 
- support w3s


