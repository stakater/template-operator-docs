# Distributing Secrets Using Sealed Secrets Template

Bill is a cluster admin who wants to provide a mechanism for distributing secrets in multiple namespaces. For this, he wants to use [Sealed Secrets](https://github.com/bitnami-labs/sealed-secrets#sealed-secrets-for-kubernetes) as the solution by adding them to Template Operator Template CR

First, Bill creates a Template in which Sealed Secret is mentioned:

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: Template
metadata:
  name: tenant-sealed-secret
resources:
  manifests:
  - kind: SealedSecret
    apiVersion: bitnami.com/v1alpha1
    metadata:
      name: mysecret
    spec:
      encryptedData:
        .dockerconfigjson: AgBy3i4OJSWK+PiTySYZZA9rO43cGDEq.....
      template:
        type: kubernetes.io/dockerconfigjson
        # this is an example of labels and annotations that will be added to the output secret
        metadata:
          labels:
            "jenkins.io/credentials-type": usernamePassword
          annotations:
            "jenkins.io/credentials-description": credentials from Kubernetes
```

Once the template has been created, Bill has to put unique label on namespaces in which the secrets have to be deployed.

Bill has added support for a new label `distribute-image-pull-secret: true"` for tenant projects/namespaces, now Template Operator will add that label depending on the used field.

Finally, Bill creates a `ClusterTemplateInstance` which will deploy the Sealed Secrets using the newly created project label and template:

```yaml
apiVersion: templates.stakater.com/v1alpha1
kind: ClusterTemplateInstance
metadata:
  name: tenant-sealed-secret
spec:
  template: tenant-sealed-secret
  selector:
    matchLabels:
      distribute-image-pull-secret: true
  sync: true
```

Template Operator will now deploy the Sealed Secrets mentioned in `Template` to namespaces which have the mentioned label. The rest of the work to deploy secret from a Sealed Secret has to be done by Sealed Secrets Controller.
