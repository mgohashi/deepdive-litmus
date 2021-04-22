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
oc apply -f https://hub.litmuschaos.io/api/chaos/1.13.3\?file\=charts/generic/experiments.yaml -n apicast210
```

# Create RBAC configuration to let Litmus access the resources

```
oc apply -f rbac.yaml
```

# Annotate your application

```
oc annotate deploy/apicast-apicast-prd litmuschaos.io/chaos="true" -n apicast210
```

# Label (For 3scale only)

This is required because the 3scale's operator doesn't have the same labels in their pods.

```
oc label deploy/apicast-apicast-prd deployment=apicast-apicast-prd
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
oc apply -f chaosengine-production.yaml -n apicast210
```

# Execute again

```
oc annotate deploy/apicast-apicast-stg litmuschaos.io/chaos="true" -n apicast210
```

```
oc apply -f chaosengine-staging.yaml -n apicast210
```

# Label (For 3scale only)

```
oc label deploy/apicast-apicast-stg deployment=apicast-apicast-stg
```

# Observe Chaos results

```
oc describe chaosresult/3scale-chaos-pod-delete -n apicast210
```

# Uninstallation

Firstly, delete any active ChaosEngines on the cluster, followed by the deletion of the Operator manifest.

```
kubectl delete chaosengine --all -n <namespace>
```

```
kubectl delete -f https://litmuschaos.github.io/litmus/litmus-operator-v1.13.3.yaml
```

