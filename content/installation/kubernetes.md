# On Kubernetes

This document contains instructions on installing, uninstalling and configuring Multi Tenant Operator on Kubernetes.

1. [Installing via Helm CLI](#installing-via-helm-cli)

1. [Uninstall](#uninstall-via-helm-cli)

## Requirements

* A **Kubernetes** cluster (v1.24 or higher)
* [Helm CLI](https://helm.sh/docs/intro/install/)
* [kubectl](https://kubernetes.io/docs/tasks/tools/)
* To run on Kubernetes, Template Operator relies on [Cert Manager](https://cert-manager.io/docs/installation/) and its Certificate CRs to create `TLS` secrets for webhooks, these certificates will be handled by templates in Helm Chart

## Installing via Helm CLI

* Public Helm Chart of Template Operator is available at [Stakater ghcr Packages](https://github.com/orgs/stakater/packages/container/package/public/charts/template-operator)

* Use `helm install` command to install Template Operator helm chart.

```terminal
helm install template-operator oci://ghcr.io/stakater/public/charts/template-operator --namespace template-operator-system --create-namespace
```

!!! note
    It is better to install Template Operator in its preferred namespace, `template-operator-system`

Wait for the pods to be up

```terminal
kubectl get pods -n template-operator-system --watch
```

## Uninstall via Helm CLI

Template Operator can be uninstalled by Helm CLI if Helm was used to install it earlier.

* Use `helm uninstall` command to remove the earlier created `Helm Release` in `template-operator-system` namespace

```terminal
helm uninstall template-operator --namespace template-operator-system
```
