# Template

A `Template` holds the resource definitions you want to distribute. It is cluster-scoped and inert on its own: nothing is created until a [TemplateInstance](template-instance.md) or [ClusterTemplateInstance](cluster-template-instance.md) references it and supplies a target.

Typical uses are initializing new namespaces with a baseline set of resources, sharing common resources across namespaces, and copying Secrets or ConfigMaps from one namespace into others.

!!! note
    `resources` and `parameters` are top-level fields on a `Template`, siblings of `metadata`, not children of `spec`. The `spec` field exists but carries nothing.

For the complete field listing, see the [API Reference](../../reference/api.md).

## Resource sources

A `Template` supplies its definitions through one of four fields: `manifests`, `helm`, `gotemplate`, or `resourceMappings`. They differ in how much templating logic they support and whether they create resources or copy existing ones.

| Field | Creates | Best for |
| --- | --- | --- |
| `manifests` | New resources from inline YAML | Static or lightly parameterized resources |
| `helm` | New resources rendered from a chart | Interdependent resources, or packaging that already exists as a chart |
| `gotemplate` | New resources from an inline Go template | Dynamic output where a full chart is more than you need |
| `resourceMappings` | Copies of existing Secrets and ConfigMaps | Sharing a resource that is already maintained elsewhere |

### Manifests

Raw Kubernetes manifests written inline. They are applied as given, with parameter substitution applied first.

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: networkpolicy
parameters:
  - name: CIDR_IP
    value: "172.17.0.0/16"
resources:
  manifests:
    - kind: NetworkPolicy
      apiVersion: networking.k8s.io/v1
      metadata:
        name: deny-cross-ns-traffic
      spec:
        podSelector:
          matchLabels:
            role: db
        policyTypes:
        - Ingress
        - Egress
        ingress:
        - from:
          - ipBlock:
              cidr: "${{CIDR_IP}}"
              except:
              - 172.17.1.0/24
          - namespaceSelector:
              matchLabels:
                project: myproject
          - podSelector:
              matchLabels:
                role: frontend
          ports:
          - protocol: TCP
            port: 6379
        egress:
        - to:
          - ipBlock:
              cidr: 10.0.0.0/24
          ports:
          - protocol: TCP
            port: 5978
```

### Helm

References a chart from a repository and renders it at deploy time. Values can be passed as a block through `values`, or individually through `setValues`.

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: redis
resources:
  helm:
    releaseName: redis
    chart:
      repository:
        name: redis
        version: 14.6.0
        repoUrl: https://charts.bitnami.com/bitnami
        username:
          key: username
          name: redis-creds
          namespace: namespace-n1
        password:
          key: password
          name: redis-creds
          namespace: namespace-n1
    setValues:
      - name: port
        value: '6379'
        forceString: false
    values: |
      redisPort: 6379
```

`releaseName` defaults to the template name if omitted. Private repositories are supported by pointing `username` and `password` at keys in an existing Secret. Each entry in `setValues` maps to a `--set` argument, or `--set-string` when `forceString` is true.

### GoTemplate

An inline Go template, using [text/template](https://pkg.go.dev/text/template) syntax with [Sprig functions](https://masterminds.github.io/sprig/) available. Template parameters are exposed as fields on the render context, so `${NAME}` in the other sources is written `{{ .NAME }}` here. The rendered output must be valid Kubernetes YAML.

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: inline-gotemplate
parameters:
  - name: Name
    value: "sample-App"
  - name: LogLevel
    value: "INFO"
resources:
  gotemplate: |
    apiVersion: v1
    kind: ConfigMap
    metadata:
      name: "{{ .Name | lower }}-config"
      labels:
        app: "{{ .Name | lower }}"
    data:
      appName: "{{ .Name }}"
      logLevel: "{{ .LogLevel }}"
      configVersion: "{{ now | date "2006-01-02" }}"
```

### Resource mapping

Copies Secrets and ConfigMaps that already exist somewhere in the cluster into the target namespaces. Only these two kinds are supported. The operator watches the source resources, so an update to a source is propagated to the copies.

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: resource-mapping
resources:
  resourceMappings:
    secrets:
      - name: secret-s1
        namespace: namespace-n1
    configMaps:
      - name: configmap-c1
        namespace: namespace-n2
```

## Parameters

Parameters let one template serve several namespaces or tenants. A template declares the parameters it accepts, and an instance supplies values for them.

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: app-config
parameters:
  - name: CIDR_IP
    value: "172.17.0.0/16"
  - name: REPLICAS
    required: true
    validation: "^[0-9]+$"
```

`value` is the default used when an instance does not supply one. A parameter marked `required` must be supplied by the instance. A `validation` pattern is a regular expression checked against the value the instance supplies.

!!! note
    An instance may only set parameters the template declares. Passing an undeclared name fails the deployment with `parameter <name> does not exist in template <template>`, which catches typos rather than letting them silently render as empty.

### Resolution order

A reference is resolved by taking the first match in this order:

1. Predefined parameters, resolved from the target namespace.
1. The value set on the instance.
1. The default `value` on the template.
1. No match, in which case the reference is left in the output verbatim as `${NAME}`.

The last case is worth remembering when a rendered resource contains a literal `${SOMETHING}`: the name was never declared as a parameter.

### Predefined parameters

These are resolved by the operator from the target namespace, so they do not need to be declared. Names are matched case-insensitively.

| Reference | Resolves to |
| --- | --- |
| `${NAMESPACE}` | Name of the namespace being rendered into |
| `${TENANT}` | Name of the tenant owning that namespace |
| `${namespace.metadata.labels.<key>}` | Value of the given label on the target namespace |
| `${namespace.metadata.annotations.<key>}` | Value of the given annotation on the target namespace |

`${TENANT}` requires Multi Tenant Operator, since it reads the tenant label the `Tenant` resource puts on the namespace. It fails if the namespace is not owned by a tenant. The label and annotation forms fail if the key is absent, rather than rendering an empty value.

### Strings and expressions

`${NAME}` substitutes into a string. `${{NAME}}` is an expression: the replacement is parsed as YAML, so the field receives a typed value.

```yaml
replicas: ${{REPLICA_COUNT}}     # renders as the number 3
tier: "${TIER_NAME}"             # renders as the string "gold"
```

The distinction matters where Kubernetes expects a non-string. Writing `replicas: "${REPLICA_COUNT}"` produces a quoted string and the resource is rejected. Expression form applies only when the whole value is exactly `${{NAME}}`, so it cannot be used for part of a longer string.

To emit a literal `${NAME}` without substitution, double the dollar sign:

```yaml
data:
  shellSnippet: "echo $${HOME}"  # renders as: echo ${HOME}
```

This is needed for config files and scripts that use the same syntax for their own variables.
