# Litmus tests

## Install the helm repo

```
helm repo add litmuschaos https://litmuschaos.github.io/litmus-helm/
```

## Install the helm chart for the Litmus operator

```
helm install chaos litmuschaos/litmus --namespace=litmus
```

## Install for specific tools

- [3scale (API Cast)](sample-3scale/README.md)
    - [x] Pod deletion
- [AMQ Broker](sample-amq-broker/README.md)
    - [x] Pod deletion
- [Nginx (Sample adapted for OCP)](sample-nginx/README.md)
    - [x] Pod deletion
