# Distributing Resources in Namespaces

Multi Tenant Operator has two Custom Resources which can cover this need using the [`Template` CR](../../concepts/templates/template.md), depending upon the conditions and preference.

1. ClusterTemplateInstance
1. TemplateInstance

## Deploying Template to Namespaces via ClusterTemplateInstances

Bill, the cluster admin, wants to deploy a docker pull secret in namespaces where certain labels exists.

First, Bill creates a template:

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: docker-secret
resources:
  manifests:
    - kind: Secret
      apiVersion: v1
      metadata:
        name: docker-pull-secret
      data:
        .dockercfg: eyAKICAiaHR0cHM6IC8vaW5kZXguZG9ja2VyLmlvL3YxLyI6IHsgImF1dGgiOiAiYzNSaGEyRjBaWEk2VjI5M1YyaGhkRUZIY21WaGRGQmhjM04zYjNKayJ9Cn0K
      type: kubernetes.io/dockercfg
```

Once the template has been created, Bill makes a `ClusterTemplateInstance` referring to the `Template` he wants to deploy with `template` field, and the namespaces where resources are needed, using `selector` field:

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: ClusterTemplateInstance
metadata:
  name: docker-secret-group-instance
spec:
  template: docker-pull-secret
  selector:
    matchExpressions:
    - key: kind
      operator: In
      values:
        - build
  sync: true
```

Afterward, Bill can see that secrets have been successfully created in all label matching namespaces.

```bash
kubectl get secret docker-secret -n bluesky-anna-aurora-sandbox
NAME             STATE    AGE
docker-secret    Active   3m

kubectl get secret docker-secret -n alpha-dave-aurora-sandbox
NAME             STATE    AGE
docker-secret    Active   2m
```

!!! note
    Deleting a `data` key from this Template removes it from every deployed Secret on the next sync, because the operator nulls `data` keys that disappear from the Template. Any other field stays on the live Secret until it is set to `null`. See [Removing a Field from a Template](removing-fields.md).

`ClusterTemplateInstance` can also target specific tenants or all tenant namespaces under a single YAML definition.
