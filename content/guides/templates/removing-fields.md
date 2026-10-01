# Removing a Field from a Template

Deleting a line from a template does not remove that field from the resources already deployed. The live resource keeps the value it was last given. To remove a field, set it to `null` and keep the line in place.

## Set the field to null

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: app
resources:
  manifests:
    - apiVersion: apps/v1
      kind: Deployment
      metadata:
        name: app
      spec:
        selector:
          matchLabels:
            app: app
        template:
          metadata:
            labels:
              app: app
          spec:
            securityContext:
              runAsNonRoot: true
              runAsUser: null      # was 65534
              seccompProfile:
                type: RuntimeDefault
            containers:
              - name: app
                image: registry.access.redhat.com/ubi9/ubi-minimal:latest
```

On the next sync the field is gone from the live resource:

```terminal
kubectl get deployment app -o jsonpath='{.spec.template.spec.securityContext}'
{"runAsNonRoot":true,"seccompProfile":{"type":"RuntimeDefault"}}
```

## Removing a whole block

A `null` on a parent key removes everything beneath it. This clears `runAsNonRoot`, `runAsUser` and `seccompProfile` in one edit:

```yaml
spec:
  template:
    spec:
      securityContext: null
```

!!! note
    Some fields are given a default once cleared, so the key may reappear as an empty value. A pod `securityContext` removed this way reads back as `{}`. The settings it held are gone.

## Lists do not need a null

An entry is removed from a list by deleting it from the template:

```yaml
env:
  - name: KEEP_ME
    value: "a"
  # DROP_ME deleted, no null needed
```

Use `null` for object fields only.

## Apply the template with server-side apply

A `null` only works if it reaches the cluster. Client-side `kubectl apply` strips it, both when creating the template and on every later apply. The stored template then looks exactly like one with the line deleted, the field is never removed, and no error is reported.

| Command | Null reaches the cluster |
| --- | --- |
| `kubectl apply --server-side` | Yes |
| `kubectl create` | Yes |
| `kubectl replace` | Yes |
| `kubectl apply` | No |

Confirm what the cluster stored:

```terminal
kubectl get template app -o yaml | grep -A3 securityContext
            securityContext:
              runAsNonRoot: true
              runAsUser: null
```

If the `runAsUser` line is absent, it was stripped on the way in. Apply again with `kubectl apply --server-side` or `kubectl replace`.

Argo CD uses client-side apply by default. For an Application that manages templates relying on a `null`, enable server-side apply:

```yaml
spec:
  syncPolicy:
    syncOptions:
      - ServerSideApply=true
```

## The instance needs sync enabled

Removing a field takes effect on the next apply, so set `sync: true` on the instance. With sync left off, the template is rendered once and never applied again, so a `null` added afterwards never reaches the deployed resources. See [Sync](../../concepts/templates/template-instance.md#sync).

!!! note
    A parameter cannot supply a `null`. Parameter values are always strings, so `runAsUser: ${UID}` with an empty value renders an empty string. Write the `null` literally in the manifest.

## Related

- [Template](../../concepts/templates/template.md)
- [TemplateInstance](../../concepts/templates/template-instance.md)
- [Sync Resources Deployed by ClusterTemplateInstance](resource-sync-by-tgi.md)
