# Ethereum Helm Charts

Ethereum Helm charts for Kubernetes — choose your `elc` and `clc` client pair and deploy to K8S. Supports `aws`, `azure`, and `local` providers.

This currently **only** supports beacon nodes, not validators.

## Values

| Key      | Type   | Default     | Description |
|----------|--------|-------------|-------------|
| provider | string | `"local"`   | Cloud provider: `local`, `aws`, `azure` |
| network  | string | `"sepolia"` | Ethereum network |
| env      | string | `"dev"`     | Environment label |
| elc      | string | `"besu"`    | Execution layer client: `besu`, `geth`, `nethermind` |
| clc      | string | `"teku"`    | Consensus layer client: `teku`, `lighthouse`, `lodestar`, `nimbus`, `prysm` |

## Examples structure

Values files are split into two layers that are composed together:

```
examples/
  clients/              # client images + global.elc/clc — shared across all networks
    besu-teku.yaml
    besu-lighthouse.yaml
    besu-lodestar.yaml
    besu-nimbus.yaml
    besu-prysm.yaml
    geth-teku.yaml
    nethermind-teku.yaml
  mainnet/base/         # storage class, tolerations, affinity for mainnet
    aws.yaml            # AWS EKS (gp3/xfs, 1000Gi ELC / 250Gi CLC)
    aws_m6a.yaml        # AWS EKS m6a instances (amd64 affinity + resource limits)
    azure.yaml          # AKS (Premium_LRS)
    local.yaml          # minimal storage for local development
  sepolia/base/         # same provider structure, smaller disks for testnet
    aws.yaml            # 500Gi ELC / 100Gi CLC
    azure.yaml
    local.yaml
```

### Requirements
1. You need `kube-prometheus-stack` or equivalent that installs the `ServiceMonitor` CRDs

```
helm install [RELEASE_NAME] oci://ghcr.io/prometheus-community/charts/kube-prometheus-stack
```

### Render / test the charts

Always pass the provider base first, then the client combo on top:

**local**
```
helm template jf ./charts/ethereum-helm-charts \
  --namespace ethereum --create-namespace \
  --values ./examples/mainnet/base/local.yaml \
  --values ./examples/clients/besu-teku.yaml
```

**aws**
```
helm template jf ./charts/ethereum-helm-charts \
  --namespace ethereum --create-namespace \
  --values ./examples/mainnet/base/aws.yaml \
  --values ./examples/clients/besu-nimbus.yaml
```

**azure**
```
helm template jf ./charts/ethereum-helm-charts \
  --namespace ethereum --create-namespace \
  --values ./examples/mainnet/base/azure.yaml \
  --values ./examples/clients/besu-teku.yaml

helm template jf ./charts/ethereum-helm-charts \
  --namespace ethereum --create-namespace \
  --values ./examples/mainnet/base/azure.yaml \
  --values ./examples/clients/geth-teku.yaml

helm template jf ./charts/ethereum-helm-charts \
  --namespace ethereum --create-namespace \
  --values ./examples/mainnet/base/azure.yaml \
  --values ./examples/clients/nethermind-teku.yaml
```

## ArgoCD / GitOps

Use an ApplicationSet with the git file generator to manage N nodes from inventory files. The ApplicationSet layers the provider base on top of the chart, and each node file supplies the client selection:

```yaml
# argocd/ethereum/nodes/mainnet/node-1.yaml
name: eth-mainnet-node-1
base: aws      # maps to examples/mainnet/base/aws.yaml
elc: besu
clc: teku
```

See the [protocols-eth-argocd](https://github.com/Consensys/protocols-eth-argocd) repo for the full ApplicationSet configuration.

## TODO
- support for validators
- support w3s
