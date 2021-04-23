# AMQ Sample

In this guide, we shall describe the steps to inject pod-delete chaos on an nginx application already deployed in the nginx namespace. If you don't have this setup you can easily create one by running these two commands:

```
oc apply -f https://hub.litmuschaos.io/api/chaos/1.13.3\?file\=charts/generic/experiments.yaml -n amq-broker
```

## Create RBAC configuration to let Litmus access the resources

```
oc apply -f rbac.yaml
```

## Annotate your application

```
oc annotate sts/amq-broker-ss litmuschaos.io/chaos="true" -n amq-broker
```

## Prepare ChaosEngine

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

## Run Chaos

```
oc apply -f chaosengine.yaml -n amq-broker
```

## Observe Chaos results

```
oc describe chaosresult/amq-broker-chaos-pod-delete -n amq-broker
```

## Uninstallation

Firstly, delete any active ChaosEngines on the cluster, followed by the deletion of the Operator manifest.

```
kubectl delete chaosengine --all -n <namespace>
```

```
kubectl delete -f https://litmuschaos.github.io/litmus/litmus-operator-v1.13.3.yaml
```
