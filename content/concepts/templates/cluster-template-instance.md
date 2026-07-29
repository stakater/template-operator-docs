# ClusterTemplateInstance

A `ClusterTemplateInstance` applies a [Template](template.md) to every namespace matching a label selector. It is cluster-scoped, and it exists for the case a [TemplateInstance](template-instance.md) cannot cover: distributing one set of resources across a group of namespaces whose membership changes over time.

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: ClusterTemplateInstance
metadata:
  name: namespace-parameterized-restrictions-tgi
spec:
  template: namespace-parameterized-restrictions
  sync: true
  selector:
    matchExpressions:
    - key: stakater.com/tenant
      operator: In
      values:
        - alpha
        - beta
  parameters:
    - name: CIDR_IP
      value: "172.17.0.0/16"
```

`spec.selector` is a standard Kubernetes label selector evaluated against namespaces, so both `matchLabels` and `matchExpressions` work. Membership is re-evaluated on every reconcile rather than resolved once at creation, which is what makes the resource useful for a set that grows.

For the complete field listing, see the [API Reference](../../reference/api.md).

## Rendering per namespace

The template is rendered separately for each matching namespace, not once and copied. Parameters that read namespace metadata therefore resolve to different values in each one. A template referencing `${NAMESPACE}` or `${namespace.metadata.labels.environment}` produces genuinely different output per target, which is usually the reason to use a selector rather than a set of individual instances.

See [Parameters](template.md#parameters) for the values available.

## How namespaces are added and removed

On each reconcile the controller compares the namespaces currently matching the selector against `status.deployedNamespaces`, the record of where it has already deployed, and sorts them into three groups.

| Group | Action |
| --- | --- |
| Matching, not yet deployed to, or previously failed | The template is rendered and applied |
| Matching and already deployed successfully | Re-rendered only when `sync` is true |
| Deployed to previously, no longer matching | The resources are removed from that namespace |

The third row is the part worth internalizing. Removing a label from a namespace, or narrowing the selector, is an instruction to delete the resources there. The operator cannot lean on garbage collection for this, since the namespaces are not its own, so it tracks them explicitly and cleans up on the next reconcile.

Namespaces that have been deleted outright are skipped rather than cleaned up, as there is nothing left to remove.

## Sync

`spec.sync` controls whether namespaces that already have the template are refreshed when the template changes.

New matching namespaces are always deployed to, with sync off or on. What sync adds is propagation of template edits to namespaces already covered. With `sync` false, each namespace receives the template as it looked when that namespace first matched, so a long-lived instance can end up with different content in different namespaces.

Cleanup of namespaces that stopped matching runs regardless of the `sync` setting.

## Ownership and deletion

Because a `ClusterTemplateInstance` is itself cluster-scoped, it can own everything it renders, both namespaced and cluster-scoped resources. Deleting the instance removes all of them through garbage collection. This is the one respect in which it is simpler than `TemplateInstance`, where cluster-scoped resources have to fall back to the `Template` as owner.

Deleting the referenced `Template` is rejected by the admission webhook while the instance still exists.

## Status

`status.deployedNamespaces` maps each namespace to its deployment state, and `status.namespaceCount` gives the number of namespaces currently matched. Mapped resources are tracked per namespace in `status.mappedSecrets` and `status.mappedConfigmaps`, so a mapping that fails in one namespace is visible without hiding successful copies elsewhere.

Overall progress is reported through `status.conditions` rather than a single status string, which is the main way the resource differs from `TemplateInstance`. Two hashes, `templateManifestsHash` and `templateResourceMappingHash`, are stored separately so that editing a template's manifests does not trigger a redistribution of its resource mappings.

## Choosing between the two

Use a `TemplateInstance` when a namespace owner should decide what lands in their own namespace, and the set of namespaces is not the point. Use a `ClusterTemplateInstance` when a platform team is enforcing something across a group of namespaces defined by labels, particularly when namespaces are created and removed regularly and each one should pick up the template without anyone acting.

The two can be used against the same template. A template is only a definition, and nothing stops one being consumed both ways.

## Related guides

- [Sync Resources Deployed by ClusterTemplateInstance](../../guides/templates/resource-sync-by-tgi.md)
- [Propagate Secrets from Parent to Descendant namespaces](../../guides/templates/copying-resources.md)
- [Deploying Private Helm Chart to Multiple Namespaces](../../guides/templates/deploying-private-helm-charts.md)
- [Distributing Secrets Using Sealed Secrets Template](../../guides/templates/distributing-secrets-using-sealed-secret-template.md)
