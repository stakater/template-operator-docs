# TemplateInstance

A `TemplateInstance` applies a [Template](template.md) to the namespace it lives in. It is the namespace-scoped counterpart of [ClusterTemplateInstance](cluster-template-instance.md), and it is the resource to reach for when a team owns a namespace and wants a standard set of resources in it.

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: TemplateInstance
metadata:
  name: networkpolicy
  namespace: build
spec:
  template: networkpolicy
  sync: true
  parameters:
    - name: CIDR_IP
      value: "172.17.0.0/16"
```

`spec.template` names a cluster-scoped `Template`. There is no namespace component, since templates are not namespaced. The API treats this field as immutable: to point a workload at a different template, delete the instance and create a new one rather than editing it in place.

Parameters listed under `spec.parameters` override the defaults declared on the template. The template must declare every parameter the instance sets, otherwise the deployment fails. See [Parameters](template.md#parameters) for resolution order and the predefined values available.

For the complete field listing, see the [API Reference](../../reference/api.md).

## Sync

`spec.sync` decides whether the instance tracks its template after the first deployment.

With `sync` left at its default of false, the template is rendered once. Once the instance reports a status, the controller returns early on every later reconcile, so subsequent edits to the template are not picked up. The deployed resources stay as they were first rendered.

With `sync: true`, the instance is reconciled whenever its template changes, and the rendered resources are brought back in line. This is comparable to running `helm upgrade` on every chart change.

Switching `sync` on later takes effect immediately. The edit triggers a reconcile, the instance is rendered again, and anything that drifted while sync was off is overwritten at that point rather than at the next template change.

This applies to resource mappings too. The controller does watch the source Secrets and ConfigMaps and will wake up when they change, but the sync check runs before the mapping work, so a non-syncing instance keeps whatever it copied the first time. Use `sync: true` on any instance whose mapped resources are expected to track their source.

## Ownership and deletion

Namespaced resources rendered by a `TemplateInstance` are given the instance as their controller owner, so deleting the instance removes them through ordinary garbage collection.

Cluster-scoped resources are the exception. A namespaced object cannot own a cluster-scoped one, so those are owned by the `Template` instead. A `ClusterRole` created through a `TemplateInstance` therefore outlives the instance and is removed only when the template is deleted.

## Status

`status.status` reports `Deployed`, `Failed`, or an empty value while the instance is still pending. On failure, `status.reason` carries a short CamelCase code and `status.message` the detail.

The remaining fields are working state rather than something to act on. `templateHash` and `templateManifests` record what was last rendered, which is how the controller ignores template updates that change nothing. `mappedSecrets` and `mappedConfigmaps` track each copied resource separately, so one failed mapping is visible without obscuring the others.

## Common failures

| `reason` | Cause |
| --- | --- |
| `TemplateNotFound` | `spec.template` names a template that does not exist |
| `ErrorReplaceParameters` | A parameter could not be resolved, or a value failed the template's `validation` pattern |
| `ErrorRenderingGoTemplate` | The `gotemplate` body failed to execute |
| `ErrorParsingGoTemplate` | The template rendered, but the output was not valid Kubernetes YAML |
| `ErrorMappingResources` | A Secret or ConfigMap named in `resourceMappings` could not be read or copied |

Two related errors surface at admission time rather than in status, because the webhook rejects the request outright: referencing a template that does not exist when creating or updating an instance, and deleting a `Template` while any instance still references it.

## Related guides

- [Distributing Resources in Namespaces](../../guides/templates/deploying-templates.md)
- [Copying Secrets and ConfigMaps across Tenant Namespaces via TGI](../../guides/templates/copying-resources-2.md)
