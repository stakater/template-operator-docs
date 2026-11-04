# Using Templates with Default Parameters

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: namespace-parameterized-restrictions
parameters:
  # Name of the parameter
  - name: DEFAULT_CPU_LIMIT
    # The default value of the parameter
    value: "1"
  - name: DEFAULT_CPU_REQUESTS
    value: "0.5"
    # If a parameter is required the template instance will need to set it
    # required: true
    # Make sure only values are entered for this parameter
    validation: "^[0-9]*\\.?[0-9]+$"
resources:
  manifests:
    - apiVersion: v1
      kind: LimitRange
      metadata:
        name: namespace-limit-range-${namespace}
      spec:
        limits:
          - default:
              cpu: "${{DEFAULT_CPU_LIMIT}}"
            defaultRequest:
              cpu: "${{DEFAULT_CPU_REQUESTS}}"
            type: Container
```

Parameters can be used with both `manifests` and `helm charts`

!!! note
    A parameter without a default `value` renders as empty when the instance leaves it out. In the `${{NAME}}` form that empty value is a `null`, and the operator removes the field from the deployed resource with no error. A default `value` prevents this. So does `required: true` with a `validation` pattern that rejects an empty string. See [Removing a Field from a Template](removing-fields.md).
