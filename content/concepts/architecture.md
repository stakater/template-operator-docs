# Architecture

Template Operator distributes Kubernetes resources across namespaces. A cluster-scoped `Template` holds the resource definitions, and an instance resource decides where those definitions are applied: a `TemplateInstance` targets the single namespace it lives in, while a `ClusterTemplateInstance` targets every namespace matching a label selector.

## API groups

The operator serves two API groups at the same time.

| Group | Kinds | Status |
| --- | --- | --- |
| `templates.stakater.com/v1alpha1` | `Template`, `TemplateInstance`, `ClusterTemplateInstance` | Current. Use this for new work. |
| `tenantoperator.stakater.com/v1alpha1` | `Template`, `TemplateInstance`, `TemplateGroupInstance` | Legacy, from when templating was part of Multi Tenant Operator. Served so existing resources keep working during migration. |

The legacy `TemplateGroupInstance` is the predecessor of `ClusterTemplateInstance`.

## Components

The operator ships as three Deployments. The two manager Deployments run the same operator image once per controller as separate containers, and an environment variable selects which controller each container starts. Running the controllers in separate containers keeps one controller's failures from stalling the others.

| Deployment | Containers | Description |
| --- | --- | --- |
| `manager` | `template-controller`, `templateinstance-controller`, `clustertemplateinstance-controller` | Reconciles the current `templates.stakater.com/v1alpha1` resources. |
| `old-manager` | `template-controller`, `templateinstance-controller`, `templategroupinstance-controller` | Reconciles the legacy `tenantoperator.stakater.com/v1alpha1` resources. Each container additionally sets `OLD_TEMPLATES_OPERATOR=true`. |
| `webhook` | `webhook-manager` | Serves the validating admission webhooks for both API groups. |

Each controller container is enabled by its own variable: `TEMPLATE_CONTROLLER`, `TEMPLATEINSTANCE_CONTROLLER`, `CLUSTERTEMPLATEINSTANCE_CONTROLLER` and `TEMPLATEGROUPINSTANCE_CONTROLLER`. Setting `ENABLE_ALL_CONTROLLERS` starts all of them in a single process instead.

## How resources are applied

When an instance is reconciled, the controller reads the referenced `Template`, resolves parameters against the target namespace, renders the resources, and applies them. Rendering depends on which field the `Template` uses: `manifests` are applied close to as written, `helm` is rendered through `helm template`, `gotemplate` is executed as a Go template, and `resourceMappings` copies existing Secrets and ConfigMaps rather than creating new definitions.

The controllers watch more than their own resource. Both instance controllers watch `Template` so that a template edit re-reconciles the instances referring to it, and they watch Secrets and ConfigMaps so that a change to a mapped source resource propagates to its copies.

To avoid reacting to updates that change nothing, the instance status stores a hash of the last rendered output. `TemplateInstance` keeps a single `templateHash`, and `ClusterTemplateInstance` keeps `templateManifestsHash` and `templateResourceMappingHash` separately, so an edit to a template's manifests does not force resource mappings to be redistributed.

## Ownership and cleanup

Cleanup runs through Kubernetes garbage collection rather than through the operator, which is why the operator's own finalizers, `templates.stakater.com/template` and `templates.stakater.com/ti`, delete nothing themselves. Each rendered resource is given a controller owner reference, and which owner it gets depends on the scope of both the resource and the instance:

| Rendered resource | Owner set by `TemplateInstance` | Owner set by `ClusterTemplateInstance` |
| --- | --- | --- |
| Namespace-scoped | The instance | The instance |
| Cluster-scoped | The `Template` | The instance |

A namespace-scoped resource cannot own a cluster-scoped one, so a `TemplateInstance` that renders something cluster-scoped, a `ClusterRole` for example, falls back to making the `Template` the owner. The resource then survives deletion of the instance and is collected only when the template itself is deleted. This is deliberate, as the alternative would be a resource nothing ever cleans up.

`ClusterTemplateInstance` cannot rely on garbage collection alone, because it writes into namespaces it does not own. It records every namespace it has deployed to in `status.deployedNamespaces` and compares that list against the current selector match on each reconcile, removing resources from namespaces that no longer match.

## Admission validation

The webhook Deployment registers validating webhooks for all six kinds across both API groups. Two rules matter in day to day use:

- Creating or updating a `TemplateInstance` or `ClusterTemplateInstance` is rejected if the referenced `Template` does not exist.
- Deleting a `Template` is rejected while any instance still references it. The error lists the referencing resources.

Admission validation is controlled by `ENABLE_WEBHOOKS` and `ENABLE_ADMISSION_WEBHOOKS` on the webhook Deployment.
