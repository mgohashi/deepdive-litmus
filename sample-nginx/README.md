# Install the helm repo

```
helm repo add litmuschaos https://litmuschaos.github.io/litmus-helm/
```

# Install the helm chart for the Litmus operator

```
helm install chaos litmuschaos/litmus --namespace=litmus
```

# In this guide, we shall describe the steps to inject pod-delete chaos on an nginx application already deployed in the nginx namespace. If you don't have this setup you can easily create one by running these two commands:

```
kubectl apply -f https://hub.litmuschaos.io/api/chaos/1.13.3\?file\=charts/generic/experiments.yaml -n nginx
```

# Create RBAC configuration to let Litmus access the resources

```
oc apply -f rbac.yaml
```

# Annotate your application

```
kubectl annotate dc/nginx litmuschaos.io/chaos="true" -n nginx
```

# Prepare ChaosEngine

Overriding these values if necessary

```yaml
...
- name: pod-delete
      spec:
        components:
          env:
            # set chaos duration (in sec) as desired
            - name: TOTAL_CHAOS_DURATION
              value: '30'

            # set chaos interval (in sec) as desired
            - name: CHAOS_INTERVAL
              value: '10'
              
            # pod failures without '--force' & default terminationGracePeriodSeconds
            - name: FORCE
              value: 'false'
```

# Run Chaos

```
oc apply -f chaosengine.yaml
```

# Observe Chaos results

```
oc describe chaosresult/nginx-chaos-pod-delete -n nginx
```

# Uninstallation

Firstly, delete any active ChaosEngines on the cluster, followed by the deletion of the Operator manifest.

```
kubectl delete chaosengine --all -n <namespace>
```

```
kubectl delete -f https://litmuschaos.github.io/litmus/litmus-operator-v1.13.3.yaml
```

