# Key Features

The key features of Template Operator are described below.

## Core Features

### Templates and Template distribution

Template Operator allows admins/users to define templates for namespaces, so that others can instantiate these templates to provision namespaces with batteries loaded. A template could pre-populate a namespace for certain use cases or with basic tooling required. Templates allow you to define Kubernetes manifests, Helm chart and more to be applied when the template is used to create a namespace.

It also allows the parameterizing of these templates for flexibility and ease of use. It also provides the option to enforce the presence of templates in one tenant's or all the tenants' namespaces for configuring secure defaults.

Common use cases for namespace templates may be:

* Adding networking policies for multitenancy
* Adding development tooling to a namespace
* Deploying pre-populated databases with test data
* Injecting new namespaces with optional credentials such as image pull secrets

More details on [Distributing Template Resources](../kubernetes-resources/how-to-guides/deploying-templates.md)

### Cross Namespace Resource Distribution

Template Operator supports cloning of secrets and ConfigMaps from one namespace to another namespace based on label selectors. It uses templates to enable users to provide reference to secrets and ConfigMaps. It uses a Cluster Template Instance to distribute those secrets and namespaces in matching namespaces, while Template Instance copies secret and configmap to a single namespace.

More details on [Copying Secrets and ConfigMaps](../kubernetes-resources/how-to-guides/copying-resources.md)

## Operational Features

### Everything as Code/GitOps Ready

Template Operator is designed and built to be 100% Kubernetes-native, and to be configured and managed the same familiar way as native Kubernetes resources so it's perfect for modern companies that are dedicated to GitOps as it is fully configurable using Custom Resources.

### Native Experience

Template Operator provides multi-tenancy with a native Kubernetes experience without introducing additional management layers, plugins, or customized binaries.
