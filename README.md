# ethnode

An Ethereum Helm chart for Kubernetes

## Values

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| network | string | `"sepolia"` |  |
| env | string | `"dev"` |  |
| elc | string | `"besu"` |  |
| clc | string | `"teku"` |  |



### Test:
```
helm template josh ./ --namespace ethereum --create-namespace --values ./examples/mainnet/besu-teku.yaml > /tmp/besu-teku-mainnet.yml
```

curl -X POST --data '{"jsonrpc":"2.0", "method":"admin_changeLogLevel", "params":["DEBUG", ["org.hyperledger.besu.ethereum.eth.manager","org.hyperledger.besu.ethereum.p2p.rlpx.connections.netty.ApiHandler","net.consensys.fleet.common.rpc.client"]], "id":1}' http://10.8.8.110:8545


## TODO
- flag for cloud/local
- test snapshot scheduler on azure/ignore on local
- add on other clients