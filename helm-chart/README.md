# Vault Secrets webhook

This chart will install a mutating admission webhook, that injects an executable to containers in a deployment/statefulset which than can request secrets from Akeyless Vault through environment variable definitions or local file on the container.

## Before you start

Before you install this chart you must create a namespace for it, this is due to the order in which the resources in the charts are applied (Helm collects all of the resources in a given Chart and it's dependencies, groups them by resource type, and then installs them in a predefined order (see [here](https://github.com/helm/helm/blob/release-2.10/pkg/tiller/kind_sorter.go#L29) - Helm 2.10).

The `MutatingWebhookConfiguration` gets created before the actual backend Pod which serves as the webhook itself, Kubernetes would like to mutate that pod as well, but it is not ready to mutate yet (infinite recursion in logic).

### Creating the namespace

The namespace must have a label of `name` with the namespace name as it's value.

set the target namespace name or skip for the default name: vswh

```bash
export WEBHOOK_NS=`<namepsace>`
```

## Installing the Chart

```bash
kubectl create namespace "${WEBHOOK_NS}"
helm install --namespace "${WEBHOOK_NS}" vault-secrets-webhook helm-chart -f ./helm-chart/values.yaml
```

## Deleting the Chart
```bash
helm delete --namespace "${WEBHOOK_NS}" vault-secrets-webhook
```

## Configuration

The following tables lists configurable parameters of the vault-secrets-webhook chart and their default values.

|               Parameter             |                    Description                    |                  Default                 |
| ----------------------------------- | ------------------------------------------------- | -----------------------------------------|
|affinity                             |affinities to use                                  |{}                                        |
|debug                                |debug logs for webhook                             |false                                     |
|image.pullPolicy                     |image pull policy                                  |Always                                    |
|image.repository                     |image repo that contains the admission server      |akeyless/k8s-webhook-server               |
|image.tag                            |image tag                                          |latest                                    |
|namespaceSelector                    |namespace selector to use, will limit webhook scope|{}                                        |
|nodeSelector                         |node selector to use                               |{}                                        |
|replicaCount                         |number of replicas                                 |1                                         |
|resources                            |resources to request                               |{}                                        |
|service.externalPort                 |webhook service external port                      |443                                       |
|service.internalPort                 |webhook service external port                      |8443                                      |
|service.name                         |webhook service name                               |secrets-webhook                           |
|service.type                         |webhook service type                               |ClusterIP                                 |
|tolerations                          |tolerations to add                                 |[]                                        |
